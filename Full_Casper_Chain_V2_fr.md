# Chaîne Casper complète v2

<!--This is the work-in-progress document describing the specification for the Casper+Sharding chain, version 2. Unlike the earlier version, which heavily relied on the existing Ethereum PoW chain as a central chain, this spec uses as its base a full proof of stake chain based on a RANDAO beacon, attestations and the Casper FFG mechanism, with a relatively simple tie to the main PoW chain, including block referencing and one-way deposits.-->

Ceci est le document de travail décrivant la spécification du la chaîne Casper+Sharding, version 2. Contrairement à la version antérieure, essentiellement fondée sur la chaîne Ethereum en preuve de travail en tant que chaîne centrale, cette spécification emploie comme base une chaîne complètement preuve d'enjeu fondée sur un phare (_beacon_) RANDAO, des attestations et le mécanisme de Casper FFG, avec un lien relativement simple vers la chaîne en PoW intégrant le référencement de blocs et des dépôts à sens unique.

### Description générale de la structure

<!--There is a central PoS chain which stores and manages the current set of active PoS validators. The only mechanism available to become a validator initially is to send a transaction on the existing PoW main chain containing 32 ETH. When you do so, as soon as the PoS chain processes that block, you will be queued, and eventually inducted as an active validator until you either  voluntarily log out or you are forcibly logged out as a penalty for misbehavior.-->

Il existe une chaîne centrale en PoS qui stocke et gère l'ensemble courant des validateurs PoS actifs. Le seul mécanisme dispobible pour devenir un validateur initialement consiste à envoyer une transaction de 32 ETH sur la chaîne principale en PoW. Ceci fait, dès que la chaîne en PoS traite ce bloc, on est mis en file d'attente et finalement accepté comme validateur actif jusqu'ầ un retrait volontaire ou forcé dans le cas d'une pénalité pour inconduite.

<!--The primary source of load on the PoS chain is **cross-links**. A cross-link is a special type of transaction that says "here is the hash of some recent block on shard X. Here are signatures from at least 2/3 of some randomly selected sample of M validators (eg. M = 1024) that attest to the validity of the cross-link". Every shard (eg. there might be 4000 shards total) is itself a PoS chain, and the shard chains are where the transactions and accounts will be stored. The cross-links serve to "confirm" segments of the shard chains into the main chain, and are also the primary way through which the different shards will be able to talk to each other.-->

La principale source de charge sur la chaîne en PoS vient des **liaisons transversales** (_cross-links_). Une référence transversale est un type de transaction qui dit «&nbsp;Voici le _hash_, l'empreinte, d'un bloc récent sur le fragment X. Voici les signatures d'au moins 2/3 d'un échantillon sélectionné aléatoirement de M validateurs (p.ex. M = 1024) qui attestent la validité de la liaison transversale.&nbsp;» Chaque fragemnt est lui-même une chaîne en PoS et les chaînes fragments sont le lieu de stockage des transactions et des comptes. Les références transversales servent à «&nbsp;confirmer&nbsp;» des segments des chaînes fragments dans la chaîne principale et sont également le moyen principal par leque les fragments peuvent communiquer entre eux.

<!--Note: the python code at https://github.com/ethereum/beacon_chain does not reflect all of the latest changes. If there is a discrepancy, this document is likely to reflect the more recent changes.-->

Note : le code Python sur https://github.com/ethereum/beacon_chain ne reflète pas l'intégralité des derniers changements. S'il existe une différence, ce document devrait refléter les dernières modifications.

### Terminologie

<!--* **Validator** - a participant in the Casper/sharding consensus system. You can become one by depositing 32 ETH into the Casper mechanism.
* **Active validator set** - those validators who are currently participating, and which the Casper mechanism looks to produce and attest to blocks, cross-links and other consensus objects.
* **Notary** - a signer of a cross-link
* **Committee** - a (pseudo-) randomly sampled subset of the active validator set. When a committee is referred to collectively, as in "this committee attests to X", this is assumed to mean "some subset of that committee that contains enough validators that the protocol recognizes it as representing the committee".
* **Proposer** - the validator that creates a block
* **Attester** - a validator that is part of a committee that needs to sign off on a block.
* **Beacon chain** - the central PoS chain that is the base of the sharding system.
* **Shard chain** - one of the chains on which transactions take place and account data is stored.
* **Main chain** - used here to refer to the existing PoW chain, though note that this is temporary; in the long run the beacon chain _will be_ the main chain.
* **Cross-link** - a set of signatures from a committee attesting to a block in a shard chain, which can be included into the beacon chain. Cross-links are the main means by which the beacon chain "learns about" the updated state of shard chains.
* **Epoch** - a period of 100 blocks.
* **Finalized** - see Casper FFG finalization here: https://arxiv.org/abs/1710.09437
* **SHARD_COUNT** - a constant referring to the number of shards. Currently set to 1024.
* **NOTARIES_PER_CROSSLINK** - a constant referring to the size of the notary set for a crosslink. Currently set to 1024.-->

* **Validateur** (_Validator_) - un participant au système de consensus Casper/sharding. On peut en devenir un en déposant 32 ETH dans le mécanisme de Casper.
* **Ensemble des validateurs actifs** (_Active validator set_) - ceux des validateurs qui participent actuellement et par lesquels le mécanisme Casper cherche à produire et à attester des blocs, des liaisons transversales et des autres objets de consensus.
* **Notaire** (_Notary_) - Un signataire d'une liaison transversale
* **Comité**  (_Committee_) - Un sous-ensemble échantillonné (pseudo-)aléatoirement de l'ensemble des validateurs actifs. Quand on se réfère collectivement à un comité, comme dans «&nbsp;ce comité atteste X&nbsp;», cela est censé signifier «&nbsp;un sous-ensemble de ce comité qui contient assez de validateurs pour que le protocole le reconnaisse comme représentant le comité&nbsp;».
* **Proposant** (_Proposer_)- Le validateur qui crée un bloc.
* **Attestataire** (_Attester_) - Un validateur faisant partie d'un comité qui doit signer un bloc.
* **Chaîne phare**  (_Beacon chain_)- La chaîne centrale en PoS qui est la base du système de fragmentation.
* **Chaîne fragment** (_Shard chain_) - L'une des chaînes sur lesquelles les transactions ont lieu et où les données des comptes sont stockées.
* **Chaîne principale** (_Main chain_) - Employé ici pour désigner la chaîne en PoW existante, bien qu'il faille noter son caractère temporaire&nbsp;; à terme, la chaîne phare _sera_ la chaîne principale.
* **Liaison transversale** (_Cross-link_) - Un ensemble de signatures d'un comité attestant d'un bloc dans une chaîne fragment, qui peut être incluse dans la chaîne phare. Les liaisons transversales représentent le principal moyen par lequel la chaîne phare «&nbsp;est mise au courant&nbsp;» de l'évolution de l'état des chaînes fragment.
* **Époque** (_Epoch_) - Une période de 100 blocs.
* **Finalisé** (_Finalized_) - Voir la finalisation de Casper ici : https://arxiv.org/abs/1710.09437
* **SHARD_COUNT** - Une constante donnant le nombre de fragments. Actuellement 1024.
* **NOTARIES_PER_CROSSLINK** - Une constante donnant la taille de l'ensemble des notaires pour une liaison transversale. Actuellement 1024.

### Changements de la chaîne principale

<!--This PoS/sharding proposal can be implemented separately from the main chain. Only two changes to the main chain are required (and the second one is technically not strictly necessary).-->

Cette proposition de PoS/Sharding peut être implémentée séparément de la chaîne principale. Seuls deux changements à la chaîne principale sont requis (et le second n'est techniquement pas nécessaire).

<!--* On the main chain a contract is added; this contract allows you to deposit 32 ETH; the `deposit` function also takes as arguments (i) `pubkey` (bytes), (ii) `withdrawal_shard_id` (int), (iii)  `withdrawal_addr` (address) and (iv) `randao_commitment` (bytes32)
* Main chain clients will implement a method, `prioritize(block_hash, value)`. If the block is available and has been verified, this method sets its score to the given value, and recursively adjusts the scores of all descendants. This allows the PoS beacon chain's finality gadget to also implicitly finalize main chain blocks.-->

* Sur la chaîne principale un contrat est ajouté&nbsp;; ce contrat vous permet de déposer 32 ETH&nbsp;; la fonction `deposit` prend en arguments (i) `pubkey`, (ii) `withdrawal_shard_id` (int), (iii)  `withdrawal_addr` (address) et (iv) `randao_commitment` (bytes32)
* Les clients de la chaîne principale implémenteront une méthode, `prioritize(block_hash, value)`. Si le bloc est disponible et a été vérifié, cette méthode donne la valeur à son score et ajuste récursivement les scores de tous les descendants. Cela permet également au gadget de finalité de la chaîne phare en PoS de finaliser implicitement les blocs de la chaîne principale.

### Chaîne phare

<!--The beacon chain is the "main chain" of the PoS system. The beacon chain's main responsibilities are:-->

La chaîne phare est la «&nbsp;chaîne principale&nbsp;» du système de PoS. Les principales responsabilités de la chaîne phare sont&nbsp;:

<!--* Store and maintain the set of active, queued and exited validators
* Process cross-links (see above) 
* Process its own block-by-block consensus, as well as the FFG finality gadget -->

* Stocker et maintenir l'ensemble des validateurs actifs, en file d'attente et partis&nbsp;;
* Traiter les liaisons transversales (voir plus haut)&nbsp;;
* Traiter son propre consensus bloc par bloc ainsi que le gadget de finalité de FFG.

<!--Here are the fields that go into every beacon chain block:-->

Voici les champs allant dans chaque bloc de chaîne phare&nbsp;:

```python
fields = {
    # Empreinte du bloc parent
    'parent_hash': 'hash32',
    # Nombre de sauts (pour le mécanisme de PoS complet)
    'skip_count': 'int64',
    # Révélation de l'engagement de Randao
    'randao_reveal': 'hash32',
    # Champ de bits de ceux du comité d'attestation qui ont participé
    'attestation_bitfield': 'bytes',
    # Leur signature agrégée
    'attestation_aggregate_sig': ['int256'],
    # Vote agrégé de fragment
    'shard_aggregate_votes': [AggregateVote],
    # Référence au bloc de la chaîne principale
    'main_chain_ref': 'hash32',
    # Empreinte de l'état
    'state_hash': 'bytes',
    # Signature du proposant
    'sig': ['int256']
}
```

<!--The beacon chain state is split into two parts, _crystallized state_ and _active state_. Active state changes every block, whereas crystallized state changes only every epoch (100 blocks). Here's active state:-->

L'état de la chaîne phare est éclatée en deux parties, _crystallized state_ et _active state_. L'état actif change à chaque bloc alors que l'état cristallisé ne change qu'à chaque époque (100 blocs). Voici l'état actif&nbsp;:

```python
fields = {
    # Hauteur du bloc
    'height': 'int64',
    # État du phare RANDAO global
    'randao': 'hash32',
    # Quels validateurs ont effectué un vote FFG pendant cette époque (en champ de bits)
    'ffg_voter_bitfield': 'bytes',
    # Attestataires du bloc à la dernière époque
    'recent_attesters': ['int24'],
    # Stocage de données sur les liaisons transversales en cours pendant cette époque
    'partial_crosslinks': [PartialCrosslinkRecord],
    # Nombre total de sauts (utilisé pour déterminer l'horodatage minimal)
    'total_skip_count': 'int64',
    # Proposants de blocs à la dernière époque
    'recent_proposers': [RecentProposerRecord]
}
```

<!--Each `PartialCrosslinkRecord` is an object containing information about crosslinks that are being put together during this epoch:-->

Chaque `PartialCrosslinkRecord` est un objet contenant des informations sur les liaisons transversales mises en place pendant cette époque&nbsp;:

```python
fields = {
    # Le fragment pour lequel la liaison transversale est construite
    'shard_id': 'int16',
    # Empreinte du bloc
    'shard_block_hash': 'hash32',
    # Ceux des votants éligibles qui votent pour lui (champ de bits)
    'voter_bitfield': 'bytes'
}
```

<!--Each RecentProposerRecord is an object containing information about recent block proposers:-->

Chaque `RecentProposerRecord` est un objet contenant des informations sur les proposants de blocs récents&nbsp;:

```python
fields = {
    # CIndex des proposants
    'index': 'int24',
    # Nouvel engagement RANDAO
    'randao_commitment': 'hash32',
    # Delta du solde
    'balance_delta': 'int24'
}
```

<!--And here's the crystallized state:-->

Et voici l'état cristallisé&nbsp;:

```python
fields = {
    # Liste des validateurs actifs
    'active_validators': [ValidatorRecord],
    # Liste des validateur inscrits mais non encore acceptés
    'queued_validators': [ValidatorRecord],
    # Liste des validateurs supprimés  en attendant un retrait
    'exited_validators': [ValidatorRecord],
    # La permutation des validateurs utilisée pour déterminer l'origine 
    # des liaisons transversales vers des fragments à cette époque
    'current_shuffling': ['int24'],
    # L'époque courante
    'current_epoch': 'int64',
    # La dernière époque justifiée
    'last_justified_epoch': 'int64',
    # La dernière époque finalisée
    'last_finalized_epoch': 'int64',
    # La dynastie courante
    'dynasty': 'int64',
    # Le fragment suivant d'où partira cette affectation de liaison transversale
    'next_shard': 'int16',
    # Le point de contrôle FFG courant
    'current_checkpoint': 'hash32',
    # Enregistrements sur la liaison transversale la plus récente pour chaque fragment
    'crosslink_records': [CrosslinkRecord],
    # Solde total des dépôts
    'total_deposits': 'int256',
    # Utilisé pour sélectionné les comités pour chaque fragment
    'crosslink_seed': 'hash32',
    # Dernière époque à laquelle la _seed_ de liaison transversale a été réinitialisée
    'crosslink_seed_last_reset': 'int64'
}
```

<!--Each ValidatorRecord is an object containing information about a validator:-->

Chaque `ValidatorRecord` est un objet contenant des informations sur un validateur&nbsp;:

```python
fields = {
    # La clef publique du validateur
    'pubkey': 'int256',
    # Le fragment auquel le solde du validateur sera envoyé
    # après le retrait
    'withdrawal_shard': 'int16',
    # Et l'adresse
    'withdrawal_address': 'address',
    # La mise en gage du validateur au phare RANDAO courant
    'randao_commitment': 'hash32',
    # Solde courant
    'balance': 'int64',
    # Dynastie où le validateur peut 
    # (être accepté | être supprimé | retirer son solde)
    'switch_dynasty': 'int64'
}
```

<!--And a CrosslinkRecord contains information about the last fully formed crosslink to be submitted into the chain:-->

Et un `CrosslinkRecord` contient des informations sur la dernière liaison transversale pleinement formée à avoir été soumise à la chaîne&nbsp;:

```python
fields = {
    # À quelle époque le liaison transversale a été soumise
    'epoch': 'int64',
    # L'empreinte du bloc
    'hash': 'hash32'
}
```

<!--The state root is equal to the concatenation of `blake(serialize(crystallized_state))` and `blake(serialize(active_state))`. Note that this means that most of the time, when the crystallized state is not changed, it does not need to be re-hashed. The active state is generally relatively small (eg. ~1 MB at the theoretical max of 4M validators, ~100kb realistically), and the crystallized state is larger, reaching into the tens of megabytes.-->

La racine de l'état est égale à la concaténation de `blake(serialize(crystallized_state))` et de `blake(serialize(active_state))`. Notons que cela signifie que, la plupart du temps, quand l'état cristallisé n'a pas changé, il n'a pas besoin d'être recalculé. L'état actif est généralement assez petit (p.ex. ~1 MB pour maximum théorique de 4M validateurs, ~100KB en étant plus réaliste) et l'état cristallisé est plus grand, atteignant des dizaines de mégaoctets.


### Traitements de la chaîne phare

<!--Processing the beacon chain is fundamentally similar to processing a PoW chain in many respects. Clients download and process blocks, and maintain a view of what is the current "canonical chain", terminating at the current "head". However, because of the beacon chain's relationship with the existing PoW chain, and because it is a PoS chain, there are differences.-->

Les traitements sur la chaîne phare est fondamentalement similaire aux traitements sur une chaîne en PoW par bien des aspects. Les clients téléchargent et traitent les blocs, et maintiennent une vue de la «&nbsp;chaîne canonique&nbsp;» courante, se terminant à la «&nbsp;tête&nbsp;» courante. Cependant, en raison des relations de la chaîne phare avec la chaîne en PoW existante et de sa nature de chaîne en PoS, il existe des différences.

<!--For a block on the beacon chain to be processed by a node, three conditions have to be met:-->

Pour qu'un bloc sur la chaîne phare puisse être traité par un nœud, trois conditions doivent être remplies&nbsp;:

<!--* The parent pointed to by the `parent_hash` has already been processed and accepted
* The main chain block pointed to by the `main_chain_ref` has already been processed and accepted
* The node's local clock time is greater than or equal to the minimum timestamp as computed by `GENESIS_TIME + height * PER_HEIGHT_DELAY + total_skips * PER_SKIP_DELAY`-->

* Le parent pointé par le `parent_hash` a déjà été traité et accepté&nbsp;;
* Le bloc de la chaîne principale vers lequel pointe `main_chain_ref` a déjà été traité et accepté&nbsp;;
* L'heure de l'horloge locale du nœud est supérieure ou égale à l'horodatage minimum tel qu'il est calculé par `GENESIS_TIME + height * PER_HEIGHT_DELAY + total_skips * PER_SKIP_DELAY`.

<!--If these three conditions are not met, the client should delay processing the block until the three conditions are all satisfied.-->

Si ces trois conditions ne sont pas remplies, le client doit retarder le traitement du bloc jusqu'à ce qu'elles le soient.

<!--Block production is significantly different because of the proof of stake mechanism. Every time the client changes its view of the head (see the section on fork choice below for how the head is defined), they should compute the proposers of the block for all `skip_count` values from 0 to M for some reasonably large M (see below for algorithms). Let `i` be the `skip_count` where the client is selected. When the timestamp reaches `minimum_timestamp(head) + PER_HEIGHT_DELAY + i * PER_SKIP_DELAY`, and if the head has not changed by then, then the client should publish a new block.-->

La production de blocs diffère significativement en raison du mécanisme de preuve d'enjeu. À chaque fois que le client change sa vue de la tête (voir la section sur le choix de la branche plus loin pour la manière dont est définie la tête), il doit calculer les proposants du bloc pour toutes les valeurs de `skip_count` de 0 à M pour un M suffisamment grand (voir les algorithmes ci-dessous). Soit `i` le `skip_count` où le client est sélectionné. Quand l'horodatage atteint `minimum_timestamp(head) + PER_HEIGHT_DELAY + i * PER_SKIP_DELAY`, et si la tête n'a pas encore changé, alors le client doit publier un nouveau bloc.

<!--When a client changes its view of the head, they should also compute the list of attesters (a set of validators that need to sign off on the head; see below for details), and immediately publish an attestation for that block, signing the serialized form of the parent block with their key.-->

Quand un lient change sa vue de la tête, il doit aussi calculer la liste des attestataires (un ensemble de validateurs qui doit approuver sur la tête&nbsp;; voir ci-dessous pour les détails) et immédiatement publier une attestation pour ce bloc en signant la forme sérialisée du bloc parent avec leur clef.

### Beacon chain fork choice rule

<!--When the beacon chain becomes a standalone PoS system, then the fork choice rule is a simple highest-scoring-block rule, where the score is the same as in Casper FFG:-->

Quand la chaîne phare deviendra un système en PoS autonome, la règle du choix de la branche sera une règle simple de bloc de plus haut score, ce score étant le même que dans Casper FFG.

`score = last_justified_epoch + height * Îµ`

<!--While it is implemented as a tag-along for the PoW chain, then the fork choice rule is a _dependent fork choice rule_: the head of the beacon chain is the highest-scoring beacon chain block with a `main_chain_ref` that is inside the highest-scoring main chain.-->
<!-- tag-along ? → https://www.urbandictionary.com/define.php?term=Tag-along -->

Tant qu'elle est implémentée comme _tag-along_ de la chaîne en PoW, la règle du choix de la branche est une _règle de choix de branche dépendante_&nbsp;: la tête de la chaîne phare est le bloc dont le score est le plus haut avec une `main_chain_ref` qui est dans la chaîne principale dont le score est le plus haut.

<!--Because of an additional validity condition that requires the `main_chain_ref` of a beacon block to be equal to or a descendant of that of its parent, this implies that the `main_chain_ref` of _every_ block in this beacon chain is inside the highest-scoring main chain). This ensures that the beacon chain follows along the actual canonical main PoW chain.-->

En raison d'une condition de validité supplémentaire qui impose que la `main_chain_ref` d'un bloc phare soit égal à ou un descendant de celle de son parent, cela implique que la `main_chain_ref` de chaque bloc dans cette chaîne phare soit à l'intérieur de la chaîne principale de plus haut score. Cela garantit que la chaîne phare suit la chaîne principale en PoW réellement canonique.

<!--This can be "updated online" using the following algorithms. First, it's important to keep track of not just the head of the main and beacon chains, but also the block at every height. This can be maintained by the following algorithm, to be run at every change of the head:-->

Cela peut être «&nbsp;mis à jour en ligne&nbsp;» grâce aux algorithmes suivants. D'abord, il est important de garder la trace non seulement de la tête des chaînes phare et principale mais également des blocs à toutes les hauteurs. Cela peut être maintenu par l'algorithme suivant, à lancer à chaque changement de la tête.

```python
def update_head(chain, old_head, new_head):
    a, b = old_head, new_head
    while a.height > b.height:
        a = get_parent(a)
    while b.height > a.height:
        b = get_parent(b)    
    while a != b:
        a, b = get_parent(a), get_parent(b)
    b = new_head
    new_chain = []
    while b.height > a.height:
        new_chain = [b] + new_chain
        b = get_parent(b)
    chain = chain[:a.height + 1] + new_chain
```

<!--When receiving a new beacon chain block, change the head only if its score is an improvement over that of the head _and_ the new block's `main_chain_ref` is in the main chain:-->

Quand on reçoit un nouveau bloc de chaîne phare, on ne change la tête que si sont score représente une amélioration par rapport à celui de la tête _et_ si la `main_chain_ref` du nouveau bloc se trouve dans la chaîne principale&nbsp;:

```python
def should_I_change_head(main_chain, old_beacon_head, new_beacon_block):
    return get_score(new_beacon_block) > get_score(old_beacon_head) and \
        new_beacon_block.main_chain_ref in main_chain
```

<!--After a reorg of the main chain, also reorg the beacon chain:-->

Après une réorganisation de la chaîne principale, on réorganise aussi la chaîne phare&nbsp;:

```python
def reorg_beacon_chain(main_chain, beacon_chain):
    old_head = beacon_chain[-1]
    while beacon_chain[-1].main_chain_ref not in main_chain:
        beacon_chain.pop()
    queue = [beacon_chain[-1]]
    new_head = beacon_chain[-1]
    while len(queue) > 0:
        b = queue.pop(0)
        for c in get_children(b):
            if c.main_chain_ref in main_chain:
                if get_score(c) > get_score(new_head):
                    new_head = c
                queue.append(c)
    update_head(beacon_chain, old_head, new_head)
```

## Fonction de transition d'état de la chaîne phare
<!-- changement ? -->

<!-- We now define the state transition function. At the high level, the state transition is made up of two parts:-->

Nous définissons maintenant la fonction de transition d'état. Au plus haut niveau, la transition d'état est constituée de deux parties&nbsp;:

<!--1. The epoch transition, which happens only if `active_state.height % SHARD_COUNT == 0`, and affects the crystallized state and the active state
2. The per-block processing, which happens every block (if during an epoch transition block, it happens after the epoch transition), and affects the active state only.-->

1. La transition d'époque, qui ne se produit que si `active_state.height % SHARD_COUNT == 0` et qui affecte l'état cristallisé et l'état actif&nbsp;;
2. Le traitement des bloc, qui se produit à chaque bloc (si, durant un bloc de transition d'époque, il se produit après la trnasition d'époque) et n'affecte que l'état actif.

<!--The epoch transition generally focuses on changes to the validator set, including adjusting balances and adding and removing validators, as well as processing cross-links and setting up FFG checkpoints, and the per-block processing generally focuses on saving temporary records relating to the in-block activity in the active state.-->

La transition d'époque est généralement centrée sur les changements de l'ensemble des validateurs, y compris l'ajustement des soldes et l'ajout et le retrait des validateurs, ainsi que le traitement des liaisons transversales et la mise en place des points de contrôle FFG, et le traitement par bloc est généralement centrée sur la sauvegarde des enregistrements temporaires liés à l'activité propre aux blocs dans l'état actif.

### Fonctions helper

<!--We start off by defining some helper algorithms. First, an algorithm for pseudorandomly shuffling the validator set based on some seed:-->

Nous commençons par définir des algoruthmes helper. D'abord, un algorithme pour mélanger pseudo-aléatoirement l'ensemble des validateurs en fonction d'une _seed_ donnée&nbsp;:

```python
def get_shuffling(seed, validator_count):
    assert validator_count <= 16777216
    rand_max = 16777216 - 16777216 % validator_count
    o = list(range(validator_count)); source = seed
    i = 0
    while i < validator_count:
        source = blake(source)
        for pos in range(0, 30, 3):
            m = int.from_bytes(source[pos:pos+3], 'big')
            remaining = validator_count - i
            if remaining == 0:
                break
            if validator_count < rand_max:
                replacement_pos = (m % remaining) + i
                o[i], o[replacement_pos] = o[replacement_pos], o[i]
                i += 1
    return o
```

<!--This algorithm will be used to select shard committees and to select proposers and attesters for blocks. Here's the application of the method to choose the attester set for a given block and the proposer for the N-skip child of that block.-->

Cet algorithme sera utilisé pour sélectionner les comités des fragments et pour sélectionner les proposants et les attestataires des blocs. Voici l'application de la méthode pour choisir l'ensemble des attestataires pour un bloc donné et le proposant pour le fils à N-sauts de ce bloc.

```python
def get_attesters_and_proposer(crystallized_state, active_state, skip_count):
    attestation_count = min(len(crystallized_state.active_validators), ATTESTER_COUNT)
    indices = get_shuffling(active_state.randao, len(crystallized_state.active_validators))
    return indices[:attestation_count], indices[attestation_count + skip_count]
```

<!--For shard crosslinks, the process is somewhat more complicated. First, we choose the set of shards active during each epoch. We want to target some fixed number of notaries per crosslink, but this means that since there is a fixed number of shards and a variable number of validators, we won't be able to go through every shard in every epoch. Hence, we first need to select which shards we will be crosslinking in some given epoch:-->

Pour les liaisons transversales entre fragments, le processus est un peu plus compliqué. D'abord nous choisissons l'ensemble des fragments actifs pendant chaque époque. Nous voulons cibler un nombre fixe de notaires par liaison transversale mais cela signifie que, puisqu'il y a un nombre fixe de fragments et un nombre variable de validateurs, nous ne pouvons pas passer par chaque fragment à chaque époque. Nous devons donc d'abord sélectinner les fragments qui feront l'objet d'une liaison transversale à une époque donnée&nbsp;:

```python
def get_crosslink_shards(crystallized_state):
    start_from = crystallized_state.next_shard
    count = len(crystallized_state.active_validators) // NOTARIES_PER_CROSSLINK
    if start_from + count <= SHARD_COUNT:
        return list(range(s, s+count))
    else:
        return list(range(start_from, SHARD_COUNT)) + list(range(start_from + count - SHARD_COUNT))
```

<!--Then we compute the notary set for any one of those shards:-->

Puis nous calculons l'ensemble des notaires pour chacun de ces fragments&nbsp;:

```python
def get_crosslink_notaries(crystallized_state, shard_id):
    crosslink_shards = get_crosslink_shards(crystallized_state)
    if shard_id not in crosslink_shards:
        return None
    all = len(crystallized_state.current_shuffling)
    start = all * crosslink_shards.index(shard_id) // len(crosslink_shards)
    end = all * (crosslink_shards.index(shard_id)+1) // len(crosslink_shards)
    return crystallized_state.current_shuffling[start: end]
```

<!--The `current_shuffling` is recalculated at the start of a dynasty transition.-->

Le `current_shuffling` est recakculé au départ d'une transition de dynastie.

### Traitement par bloc

<!--The per-block processing is broken down into three parts.-->

Le traitement des blocs se décompose en trois parties.

#### Vérification des signatures des attestations

<!--The expected number of attesters for a block is `min(len(crystallized_state.active_validators), ATTESTER_COUNT)`; let this value be `attester_count`. Check that the `attestation_bitfield` as a bytearray of length `(attester_count + 7) // 8`. Interpret it as a list of bits going left to right (eg. bit 0 is `attestation_bitfield[0] >> 7`, bit 9 is `attestation_bitfield[1] >> 6`, etc). Check that any bits after `attester_count-1` are all zero. Check that the total number of attesters (ie. the total number of one bits) is at least `attester_count / (2 + block.skip_count)`.-->

Le nombre attendu d'attestataires pour un bloc est `min(len(crystallized_state.active_validators), ATTESTER_COUNT)`, cette valeur étant `attester_count`. On  vérifie que le tableau d'octets `attestation_bitfield` de longueur `(attester_count + 7) // 8`.**????** On l'interprète comme une liste de bits allant de gauche à droite (c.à.d. que le bit 0 est `attestation_bitfield[0] >> 7`, bit 9 est `attestation_bitfield[1] >> 6`, etc). On vérifie que tous les bits après`attester_count-1` sont à zéro. On vérifie que le nombre total d'attestateurs (c.à.d. le nombre total de bits à 1 est au moins `attester_count / (2 + block.skip_count)`.

<!--Interpret the one bits in the bitfield as a subset of the attesters sampled with `get_attesters_and_proposer` (see above); extract the indices of the attesters and then from `crystallized_state.active_validators` extract their public keys. Add up these public keys to make an aggregate key. BLS verify the `attestation_aggregate_sig` in the block using the aggregate key as a pubkey, and the serialized parent block as a message. Make sure that the verification passes.-->

On interprète les bits à un dans le champ de bits comme un sous-ensemble des attestateurs échantillonné avec `get_attesters_and_proposer` (voir plus haut)&nbsp;; on extrait les indices des attestateurs puis on extrait leurs clefs publiques depuis `crystallized_state.active_validators`. On additionne ces clefs publiques pour fabriquer une clef agrégée. On vérifie par BLS <!-- Boneh–Lynn–Shacham --> l'`attestation_aggregate_sig` dans le bloc en utilisant la clef agrégée comme une clef publique et le bloc parent sérialisé comme le message. On s'assure que la vérification passe.

<!--Add a `ProposerRecord` to the `recent_proposers` list storing the proposer index, the proposer's new RANDAO preimage and the number of attesters whose signatures they included. Add the indices of the attesters (in the order in which they appear in the bitfield) to `recent_attesters`.-->

On ajoute un `ProposerRecord` à la liste `recent_proposers` stockant l'index des proposants, la nouvelle préimage RANDAO du proposant et le nombre d'attestataires dont sont inclues les signatures. On ajoute les indices des attestataires (dans l'ordre dans lequel ils apparaissent dans le champ de bits) à `recent_attesters`.

#### Vérification des enregistrements de liaisons transversales partielles

<!--A block can have 0 or more `AggregateVote` objects, where each `AggregateVote` object has the following fields:-->

Un bloc peut avoir 0 ou plus objets `AggregateVote`, où chaque objet `AggregateVote` possède les champs suivants&nbsp;:

```python
fields = {
    'shard_id': 'int16',
    'shard_block_hash': 'hash32',
    'notary_bitfield': 'bytes',
    'aggregate_sig': ['int256']
}
```

<!--For each one of these votes:-->

Pour chacun de ces votes&nbsp;:

<!--* Use `get_shard_attesters` to get the crosslink attester index list. Verify the aggregate sig against the notary bitfield in the same way as done for the block attesters above. 
* If a `PartialCrosslinkRecord` object already exists for the same shard ID and hash, add into its `voter_bitfield` the local indices of the validators that participated in the `AggregateVote` (that is, `voter_bitfield |= AggregateVote.notary_bitfield`). If a `PartialCrosslinkRecord` does not yet exist, create one.
* Also add the indices of the voters into the `active_state.ffg_voter_bitfield`
* Add a balance delta giving the block proposer a reward of n if there are n total voters that have not yet voted.-->

* On utilise `get_shard_attesters` pour obtenir la liste des indices des attestataires de liaisons transversales. On vérifie la signature agrégée pour les attestataires de blocs ci-dessus&nbsp;;
* Sur un objet `PartialCrosslinkRecord` existe déjà pour les mêmes ID et empreinte de bloc, on ajoute à son `voter_bitfield` les indices locaux des validateurs qui ont participé au `AggregateVote` (c'est-à-dire `voter_bitfield |= AggregateVote.notary_bitfield`). Si un `PartialCrosslinkRecord` n'existe pas encore, on en crée un&nbsp;;
* On ajoute aussi les indices des votants dans le `active_state.ffg_voter_bitfield`&nbsp;;
* On ajoute un delta de solde donnant au proposant du bloc une récompense de n s'il y a un total de n votants qui n'ont pas encore voté.

<!--Save the new list of `PartialCrosslinkRecord` objects in order sorted by `shard_block_hash`.-->

On sauvegarde la nouvelle liste d'objets `PartialCrosslinkRecord` dans l'ordre trié par `shard_block_hash`.

#### Divers

<!--* Increase height by 1
* Verify the signature on the block itself, using the block with the signature set to all zero bytes as signing data
* Increase the total skip count by the block's skip count
* Verify that the block's RANDAO reveal is the hash preimage of the proposer's saved RANDAO commitment. XOR the block's RANDAO reveal into the active RANDAO state, and set the RANDAO reveal to be the proposer's new commitment-->

* On incrémente la hauteur;&nbsp;
* On vérifie la signature du bloc lui-même en utilisant le bloc avec les octets de signature mis à zéro comme données de signature&nbsp;;
* On incrémente le décompte total de saut du nombre de sauts du bloc&nbsp;;
* On vérifie que la révélation RANDAO du bloc est la préimage de l'empreinte de la mise en gage RANDAO sauvegardée du proposant. On XOR la révélation RANDAO du bloc dans l'état RANDAO actif et la révalation RANDAO devient la nouvelle mise en gage du proposant.

### Transitions d'époques

<!--When the current block height (ie. the height in the active state) is 0 mod SHARD_COUNT, we execute an epoch transition, which breaks down into several parts:-->

Quand la hauteur de bloc courante (c.à.d. la hauteur de l'état actif) est à 0 mod SHARD_COUNT, nous exécutons une transition d'époque qui se décompose en plusieurs parties&nbsp;:

#### Calculer les récompenses pour les votes FFG

<!--* Compute the total deposits of every validator who participated in the last epoch (according to the FFG voter bitfield in the active state). If this value is >= 2/3 of the total deposits of all validators, set `crystallized_state.justified_epoch` to equal `crystallized_state.current_epoch` (note: this is still the previous epoch at this point in the computation). If this happens, and the justified epoch was previously `crystallized_state.current_epoch - 1`, set the `crystallized_state.finalized_epoch` to equal that value.
* Compute the `online_reward` and `offline_penalty` based on the Casper FFG incentive and quadratic leak rules (not yet fully specified)
* Add the `online_reward` to every validator who participated in the last epoch, and subtract the `offline_penalty` from everyone who did not-->

* Calculer les dépôts totaux de chaque validateur ayant participé pendant la dernière époque (d'après le champ de bits des votants FFG dans l'état actif). Si cette valeur est ≥ 2/3 des dếpôts totaux de tous les validateurs, `crystallized_state.justified_epoch` est mis à `crystallized_state.current_epoch` (note&nbsp;: il s'agit toujours de l'époque précédente à ce point du calcul). Si celà se produit, et si l'époque justifiée était précédemment `crystallized_state.current_epoch - 1`, `crystallized_state.finalized_epoch` devient égal à cette valeur.
* Calculer les `online_reward` et `offline_penalty` en se basant sur les incitations de Casper FFG et les _quadratic leak rules_ (restent à spécifier complètement)&nbsp;;
* Ajouter la `online_reward` à chaque validateur qui a participé pendant l'époque précédente et soustraire la `offline_penalty` de ceux qui n'ont pas participé.

### Calculer les récompenses pour les liaisons transversales

Répéter pour chaque fragment&nbsp;:

<!--* Calculate the `online_reward` and `offline_penalty` for that crosslink.
* Take the partial crosslink with the most votes (breaking ties by order of checkpoint hash). Reward any validator that participated in that partial crosslink; penalize any validator that did not.
* If any crosslink reaches >=2/3 of its sample, weighted by total deposits (not including any balance deltas that are part of this epoch transition), save it as the most recent crosslink-->

* Calculer les `online_reward` et `offline_penalty` pour cette référence transversale&nbsp;;
* Prendre la référence transversale partielle avec le plus de vote (en cassant les liaisons par ordre d'empreinte de point de contrôle). Récompenser tous les validateurs qui ont participé à cette liaison transversale partielle&nbsp;; pénaliser les autres.
* Si une liaison transversale atteint ≥ 2/3 de son échantillon, redressé par les dépôts totaux (en excluant les deltas de solde qui font partie de cette transition d'époque), on le sauvegarde comme la liaison transversale la plus récente.

### Traiter les deltas de soldes

<!--Increment the balance of everyone in `recent_attesters` by 1. Increment the balance of everyone in `recent_proposers` by the `balance_delta` in the `RecentProposerRecord` object.-->

Incrémenter le solde de tous les `recent_attesters` de 1. Incrémenter le solde de tous les `recent_proposers` de `balance_delta` dans l'objet `RecentProposerRecord`.

### Calculs liés aux _seed_ des liaisons transversales <!--Crosslink seed-related calculations-->

<!--If `current_epoch - crosslink_seed_last_reset` is an exact power of two, then using `temp_seed = blake(crosslink_seed + bytes([log2(current_epoch - crosslink_seed_last_reset)]))`, set  `current_shuffling = get_shuffling(temp_seed, len(new_active_validators))`. Note that all of the temp committees are predictable from the `crosslink_seed`, but their positioning stretches out exponentially (eg. 10001 to 10002, 10002 to 10004, 10004 to 10008, 10008 to 10016...).-->

Si `current_epoch - crosslink_seed_last_reset` est une exacte puissance de deux, alors en utilisant `temp_seed = blake(crosslink_seed + bytes([log2(current_epoch - crosslink_seed_last_reset)]))` on pose `current_shuffling = get_shuffling(temp_seed, len(new_active_validators))`. Notons que tous ceux des comités temporaires sont prévisibles par la `crosslink_seed` mais leur positionnement augmente exponentiellement (c.à.d. de 10001 à 10002, de 10002 à 10004, de 10004 à 10008, de 10008 à 10016…).

<!--The exponential backoff ensures that, if there has not been a crosslink for N epochs, the next committee will have ~N/2 epochs to process through and verify the ~N epochs worth of history for that shard, so the amount of work per second that the committee nodes need to perform is bounded.-->

Le _backoff_ exponentiel <!-- https://fr.wikipedia.org/wiki/Binary_exponential_backoff --> garantit que, s'il n'y a pas eu de liaison transversale pendant N époques, le comité suivant aura ~N/2 époques pour traiter et vérifier les ~N époques d'historique pour ce fragment, ce qui signifie que la quantité de travail par seconde que les nœuds du comité doivent effectué est limité.

### Transistion de dynastie

<!--If the following two conditions are both true:-->

Si les conditions suivantes sont toutes les deux vraies&nbsp;:

<!--* The last two epochs were both justified
* The height of the most recent recorded crosslink for every shard is greater than or equal to `crosslink_seed_last_reset`-->

* Les deux dernières époques étaient justifiées&nbsp;;
* La hauteur de la liaison transversale la plus récemment enregistrée pour chaque fragment est plus grande ou égale à `crosslink_seed_last_reset`.

Alors&nbsp;:

<!--* Set `crystallized_state.dynasty += 1`
* Set `crosslink_seed = active_state.randao`
* Go through all `queued_validators`, in order from first to last. Any validators whose `switch_dynasty` is equal to or earlier than the new dynasty are immediately added to the end of `active_validators` (setting `switch_dynasty` to the highest possible integer), up to a maximum of `(len(crystallized_state.active_validators) // 30) + 1` (notice that because `queued_validators` is sorted, it suffices to check the queued validators at the beginning until either you've processed that many, or you reach one whose `switch_dynasty` is later than the current dynasty)
* Go through all `active_valdidators`, and move any with either (i) balance equal to <= 50% of their initial balance, or (ii) `switch_dynasty` equal to or less than the new current dynasty, to `exited_validators`, moving up to a maximum of `(len(crystallized_state.active_validators) // 30) + 1` validators-->

* Incrémenter `crystallized_state.dynasty`&nbsp;;
* Passer par tous les `queued_validators` dans l'ordre du premier au dernier. Tout validateur dont `switch_dynasty` est égal où antérieur à la nouvelle dynastie est immédiatement ajouté à la fin de `active_validators` (en mettant `switch_dynasty` à l'entier le plus élevé possible) jusqu'à un maximum de `(len(crystallized_state.active_validators) // 30) + 1` (remarquez que comme `queued_validators` est trié, il suffit soit de vérifier les validateurs en file d'attente au départ jusqu'à ce que vous en ayez traité suffisamment, soit d'en atteindre un dont le `switch_dynasty` soit plus avancé que la dynastie courante)&nbsp;;
* Passer par tous les  `active_valdidators` et déplacer ceux dont (i) le solde est égal à ≤ 50% de leur solde initial, ou (ii) `switch_dynasty` est égal ou inférieur à la dynastie courante, vers `exited_validators`, en montant à un maximum de `(len(crystallized_state.active_validators) // 30) + 1` validateurs.

### Divers

<!--* Increment the current epoch
* Set the `current_checkpoint` to the hash of the previous block
* Reset the balance deltas, FFG voter bitfield and partial crosslink list
* Update the active validators' RANDAO commitments based on the `RecentProposerRecord` values.-->

* Incrémenter l'époque courante&nbsp;;
* Donner à `current_checkpoint` la valeur de l'empreinte du bloc précédent&nbsp;;
* Réinitialiser les deltas de soldes, le champ de bits des votants FFG et la liste des liaisons transversales partielles&nbsp;;
* Mettre à jour les mises en gage RANDAO des validaeurs d'après les valeurs de `RecentProposerRecord`.

## Analyse de l'overhead <!--indirect ?-->

<!--With one validator per 32 ETH, we can look at three cases:-->

Avec un validateur par 32 ETH, trois cas se présentent à nous&nbsp;:

<!--* 31250 validators (1M ETH), minimal case
* 312500 validators (10M ETH), average case
* 4000000 validators (assuming 128M ETH as a supply cap), worst case: literally everyone stakes-->

* 31250 validateurs (1M ETH), cas minimal&nbsp;;
* 312500 validateurs (10M ETH), cas moyen&nbsp;;
* 4000000 validateurs (en posant 128M ETH comme limite d'émission), au pire des cas&nbsp;: absolument tout le monde mise.

<!--The size of the active state is:-->

La taille de l'état actif est&nbsp;:

```python
'height': 8 octets
'randao': 32 octets,
'ffg_voter_bitfield': 1/8 octets par validateur,
'recent_attesters': 3 octets/attestataire, ~130 attestataires/bloc, 100 blocs/époque:
                    39000 octetx
'partial_crosslinks': PartialCrosslink * (nombre de validateurs / 1024)
'total_skip_count': 8 octets
'recent_proposers': (3 + 32 + 3) octets par RecentProposerRecord *
                    100 proposants = 3800 octets
```

<!--The partial crosslinks add:-->

Les liaisons transversales ajoutent&nbsp;:

```python
'shard_id': 2 octets,
'shard_block_hash': 32 octets,
'voter_bitfield': 1/8 octet/validateur * 1024 validateurs = 128 octets
```

<!--Each crosslink is thus 162 bytes, and the fixed data is 42848 bytes. Per validator, we get 1/8 bytes in the FFG bitfield, and 162/1024 ~= 0.158 bytes per validator in the partial crosslink,a  total of ~0.283 bytes per validator. Hence, we get as the total active state size:-->

Chaque liaison transversale fait donc 162 octets et les données fixes 42848 octets. Par validateurs, nous avons 1/8 d'octet dans le champ de bits FFG et 162/1824 ~= 0.158 octets par validateur dans la liaison transversale partielle, un total de ~0.283 octet par validateur. Nous obtenons donc pour la taille totale de l'état actif&nbsp;:

* 1M ETH: 42848 + 31250 * 0.283 = 51692 octets
* 10M ETH: 42848 + 312500 * 0.283 = 131286 octets
* 128M ETH: 42848 + 31250 * 0.283 = 1174848 octets

<!--The size of the crystallized state is:-->

La taille de l'état cristallisé est&nbsp;:

```python
'active_validators': [ValidatorRecord],
'queued_validators': [ValidatorRecord],
'exited_validators': [ValidatorRecord],
'current_shuffling': 3 octets par validateur,
'current_epoch': 8 octets,
'last_justified_epoch': 8 octets,
'last_finalized_epoch': 8 octets,
'dynasty': 8 octets,
'next_shard': 2 octets,
'current_checkpoint': 32 octets,
'crosslink_records': [CrosslinkRecord],
'total_deposits': 32 octets,
'crosslink_seed': 32 octets,
'crosslink_seed_last_reset': 8 octets
```

<!--That's a fixed cost of 141 bytes, not including the crosslinks and validators.-->

Cela représente un coût fixe de 141 octets hors liaisons transversales et validateurs.

<!--A ValidatorRecord contains:-->

Un ValidatorRecord contient&nbsp;:

```
'pubkey': 32 octets,
'withdrawal_shard': 2 octets,
'withdrawal_address': 20 octets,
'randao_commitment': 32 octets,
'balance': 8 octets,
'switch_dynasty': 8 octets
```

<!--That's 102 bytes in total. We'll assume that the queued and exited sets are empty for simplicity and focus on the active set.-->

Ce qui fait 102 octets au total. Nous supposerons que les ensemble en file d'attente et en instance de sortie sont vides dans un but de simplicité et nous nous concentrons sur l'ensemble actif.

<!--A CrosslinkRecord is just:-->

Un CrosslinkRecord est simplement&nbsp;:

```
'epoch': 8 octets,
'hash': 32 octets
```

<!--So the `crosslink_records` list is just 40 bytes per shard. The total number of shards is determined by the maximum number of validators: 128 million / 32 / 1024 ~= 4000 shards, so `crosslink_records` would take up 160000 bytes.-->

Donc la liste `crosslink_records` ne fait que 40 octets par fragment. Le nombre total de fragments est déterminé par le nombre maximum de validateurs&nbsp;: 128 millions / 32 / 1024 ~= 4000 fragments et `crosslink_records` prendrait donc 160000 octets.

<!--Hence, with a fixed cost of 160141 bytes and a variable cost of 102 bytes per validator, we get as the total crystallized state size:-->

D'où, avec un coût fixe de 160141 octets et un coût variable de 102 octets par validateur, nous obtenons une taille totale de l'état cristallisé&nbsp;:

* 1M ETH: 160141 + 102 * 31250 = 3347641 octets
* 10M ETH: 160141 + 102 * 312500 = 32035141 octets
* 128M ETH: 160141 + 102 * 4000000 = 408160141 octets

<!--So in even the most extreme case, the beacon chain state can fit into even a relatively old computer's RAM. Blake hashing 400 MB only takes ~1.0 seconds when tested on my machine (Thinkpad X270), a very acceptable worst-case result for something that is expected to only happen once per 100 blocks, though we can increase efficiency here further with judicious use of Merkle trees.-->

Donc même dans le cas le plus extrême, l'état de la chaîne phare peut tenir dans la RAM d'un ordinateur relativement vieux. Un hachage Blake de 400 Mo ne prend que ~1,0 seconde lors de tests sur ma machine, (Thinkpad X270), un résultat de pire des cas très acceptable pour quelque chose qui n'est censé se produire que tous les 100 blocs, bien que nous puissions encore améliorer l'efficacité ici par l'emploi judicieux d'arbres de Merkle.

<!--A block contains:-->

Un bloc contient&nbsp;:

```
'parent_hash': 32 octets,
'skip_count': 8 octets,
'randao_reveal': 32 octets,
'attestation_bitfield': 1/8 octet par participant, donc 16 octets,
'attestation_aggregate_sig': 64 octets,
'shard_aggregate_votes': [AggregateVote],
'main_chain_ref': 32 octets,
'state_hash': 64 octets,
'sig': 64 octets
```

<!--An AggregateVote is:-->

Un AggregateVote est&nbsp;:

```
'shard_id': 2 octets,
'shard_block_hash': 32 octets,
'notary_bitfield': 1/8 octet/validateur * 1024 validateurs,
'aggregate_sig': 64 octets
```

<!--A block without aggregate votes sums up to 312 bytes. Each aggregate vote is 226 bytes; we expect validator count / 1024 aggregate votes per epoch (and 1/100 that per block), so we get as the average block size:-->

Un bloc sans les votes agrégés va jusqu'à 312 octets. Chaque vote agrégé fait 226 octets&nbsp;; nous attendons nombre de validateurs / 1024 votes agrégés par époque (et 1/100 de cela par bloc), et nous obtenons pour la taille moyenne des blocs&nbsp;:

* 1M ETH: 312 + 31250 * 226 / 1024 / 100 = 380 octets
* 10M ETH: 312 + 312500 * 226 / 1024 / 100 = 1002 octets
* 128M ETH: 312 + 4000000 * 226 / 1024 / 100 = 9140 octets

<!--Block processing requires:-->

Le traitement des blocs demande&nbsp;:

<!--* Re-hashing active state
* Re-hashing crystallized state (in an epoch transition only)
* Verifying 1 BLS signature for the block, plus 1 for the attestations, plus 1 for each crosslink record. Best case: 2.30 per block, average case 5 per block, worst case: 41 per block
* 2 ECADD operations per epoch (ie. 0.02 per block) per validator for aggregate sig verification, plus a fixed ~128 ECADD operations per block. Best case ~753 (equivalent to <1 ECDSA sig), average case ~6378 (~5-10 ECDSA sigs), worst case ~80128 (~50-100 ECDSA sigs)
* Many relatively cheap operations involving looping over validators, filling in bitfields, etc etc.-->

* Le re-hachage de l'état actif&nbsp;;
* Le re-hachage de l'état cristallisé (uniquement lors d'une transition d'époque)&nbsp;;
* La vérification d'une signature BLS pour le bloc, plus une pour les attestations, plus une pour chaque enregistrement de liaison transversale. Au meilleur des cas 5 par bloc, au pire 41 par bloc&nbsp;;
* 2 opérations ECADD par époque (c.à.d. 0.02 par bloc) par validateur pour la vérification des signatures agrégées, plus un nombre fixe de ~128 opérations ECADD par bloc. Au meilleur des cas ~753 (équivalent à <1 signature ECDSA), en moyenne <!--général--> ~6378 (~5-10 signatures ECDSA), au pire ~80128 (~50-100 signatures ECDSA)&nbsp;;
* Beaucoup d'opérations relativement peu coûteuses impliquant de boucler sur les validateurs, filtrer sur les champs de bits, etc. etc.

-------

<!--Note: this is ~80% complete. Main sections missing are:

* Logic for the formats of shard chains, who proposes shard blocks, etc (in an initial release, if desired we could make cross-links just be Merkle roots of blobs of data; in any case, one can philosophically view the whole point of the shard chains as being a coordination device for choosing what blobs of data to propose as crosslinks)
* Logic for inducting queued validators from the main chain
* Penalties for signing or attesting to non-canonical-chain blocks (update: may not be necessary, see https://ethresear.ch/t/attestation-committee-based-full-pos-chains/2259)
* Slashing conditions
* Logic for withdrawing deposits to shards
* Per-validator proofs of custody-->

Note : Ceci est complet à 80%. Les sections principales qui manquent sont&nbsp;:

* La logique des formats des chaînes fragments, qui propose les blocs des fragments, etc. (dans une version initiale, nous pourrions au besoin nous contenter de racines d'arbres de Merkle de blobs de données pour faire des liaisons transversales&nbsp;; en tous les cas, on peut voir philosophiquement le principe fondamental des chaînes fragments comme un outil de coordination servant à choisir les blobs de données à proposer comme liaisons transversales)&nbsp;;
* La logique d'acceptation des validateurs en attente de la chaîne principale&nbsp;;
* Les conditions de _slashing&nbsp;_;
* La logique de retrait des dépôts vers les fragments&nbsp;;
* Les preuves de détention par validateur.

