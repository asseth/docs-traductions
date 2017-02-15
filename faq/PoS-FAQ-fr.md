# FAQ - Proof of Stake

### Qu’est-ce que la preuve d’enjeu ou _Proof of Stake_&nbsp;?

La preuve d’enjeu est un algorithme de consensus pour blockchains publiques qui
vise à servir d’alternative au mécanisme dit de preuve de travail (_Proof of
Work_). La preuve de travail est le mécanisme qui sous-tend la sécurité des
protocoles de Bitcoin, de la version actuelle d’Ethereum et de beaucoup
d’autres blockchains. Ce mécanisme a cependant été critiqué pour le coût en
électricité qui découle du processus de minage et son impact environnemental dû
à la pollution générée par les sources d’énergies utilisées par certaines
centrales de minage. Il a notamment été calculé que le protocole de preuve de
travail du Bitcoin 
[consomme annuellement autant d’électricité que l’Irlande entière](https://karlodwyer.github.io/publications/pdf/bitcoin_KJOD_2014.pdf). 
L’algorithme de
preuve d’enjeu tente de résoudre ces problèmes en supprimant entièrement le
concept de minage et en le remplaçant par un autre mécanisme.

Le mécanisme de la preuve d’enjeu peut être décrit comme un «&nbsp;minage
virtuel&nbsp;». Là où la preuve de travail prévient efficacement 
[les attaques Sybil](https://en.wikipedia.org/wiki/Sybil_attack) 
en se
fondant sur la rareté et le coût du matériel informatique, la preuve d’enjeu
repose sur la crypto-monnaie de la blockchain elle-même. Avec la preuve de
travail, un participant peut investir 1000 dollars dans un ordinateur de
minage, le brancher, commencer à participer au réseau en produisant des blocs
et recevoir une récompense. Avec la preuve d’enjeu, le même participant
investit 1000 dollars en achetant directement la crypto-monnaie de la
blockchain puis met en dépôt ces crypto-monnaies en utilisant le mécanisme de
preuve d’enjeu, qui va ensuite (pseudo-)aléatoirement assigner à ce participant
le droit de produire des blocs et de recevoir une récompense. A l’instar du
mécanisme de preuve de travail, où investir 2000 dollars vous permet d’avoir
un ordinateur de minage deux fois plus puissant et qui peut produire des blocs
deux fois puis efficacement, un dépôt deux fois plus important a deux fois plus
de chance d’être sélectionné pour produire des blocs dans un intervalle de
temps donné.

En général, un algorithme de preuve d’enjeu se présente comme suit. Il existe
un certain nombre de possesseurs de crypto-monnaies qui les mettent en dépôt
dans le cadre du mécanisme de preuve d’enjeu&nbsp;: ils deviennent des
validateurs. Sur la base de la «&nbsp;tête&nbsp;» de la blockchain (c’est à
dire le dernier bloc de la blockchain), l’algorithme sélectionne aléatoirement
un validateur (l’aléa étant pondéré par le montant total déposé, par exemple un
validateur avec 10 000 crypto aura dix fois plus de chance d’être sélectionné
qu’un validateur avec 1 000 crypto) et lui attribue le droit de créer le
prochain bloc. Si ce validateur ne crée pas le bloc dans un intervalle de temps
donné, un deuxième validateur est sélectionné, ainsi de suite. Tout comme dans
le mécanisme de preuve de travail, la chaîne la plus longue est par défaut
considérée comme la chaîne valide (ou «&nbsp;canonique&nbsp;»).

Il existe de nombreuses variations de ce modèle. Dans les premiers algorithmes
de type Peercoin, un droit de validation était attribué à un validateur
différent toute les secondes. Parfois, il n’existe aucun mécanisme explicite
pour «&nbsp;devenir un validateur&nbsp;». Chaque détenteur de crypto-monnaie
est potentiellement un validateur et si ledit détenteur est hors ligne ou pas
intéressé à valider, il peut «&nbsp;passer son tour&nbsp;». Dans certains
algorithmes, il n’existe aucune notion de sélection de validateur&nbsp;; il
existe à la place un algorithme traditionnel 
[résistant aux failles byzantines](https://en.wikipedia.org/wiki/Byzantine_fault_tolerance) 
qui est utilisé pour permettre à _tous_ les validateurs
de se mettre d’accord sur le prochain bloc. La source de l’algorithme
pseudo-aléatoire qui permet de choisir le prochain validateur peut aussi être
différente. Le principe d’utiliser des crypto-monnaies, en dépôt ou non, en
substitut des mineurs est cependant la caractéristique invariable de ces
différents mécanismes.

### Quels sont les avantages de la preuve d’enjeu?

En résumé:

* Aucune nécessité de consommer de grandes quantités d’électricité pour
sécuriser une blockchain.  * Du fait de l’absence de consommation significative
d’électricité, il n’y a pas autant besoin d’émettre de nouvelles
crypto-monnaies pour motiver les participants à contribuer au réseau. Il est
même possible, en théorie, de prévoir une émission nette négative de crypto,
par exemple si une fraction des frais de transactions est «&nbsp;brûlée&nbsp;».
La quantité totale de crypto est alors décroissante.  
* Réduction potentielle de la vulnérabilité à l’attaque dite «&nbsp;minage
égoïste&nbsp;» (_selfish mining_) avec la «&nbsp;théorie des jeux
coopératifs&nbsp;», même s’il est possible d'implémenter partiellement un
mécanisme similaire en preuve de travail.  
* Réduction des risques de centralisation, dans la mesure où les économies
d’échelle constituent moins un problème. 10 millions d’une crypto-monnaie vous
donnent exactement 10 fois plus de retour sur investissement qu’un million,
sans que n’entre en jeu l’avantage disproportionné que permet d’atteindre la
production en masse de matériel de minage par exemple.  * Possibilité
d’utiliser des pénalités économiques pour rendre de nombreuses formes
d’attaques à 51 % beaucoup plus coûteuses qu’en utilisant le mécanisme de
preuve de travail - pour paraphraser Vlad Zamfir, «&nbsp;_participer à une
attaque à 51 % revient à faire brûler votre ferme d’ASIC_&nbsp;».

### Comment la preuve d'enjeu s'insère-t-elle dans la recherche traditionnelle de tolérance aux failles byzantines&nbsp;?

Il existe plusieurs résultats fondamentaux issus de la recherche sur la
tolérance aux failles byzantines qui s’appliquent à tous les algorithmes de
consensus, y compris les algorithmes de consensus traditionnels comme PBFT. Ils
s’appliquent aussi à tous les algorithmes de preuve d’enjeu et même
(contrairement à l’opinion générale) dans une certaine mesure à des algorithmes
de preuve de travail.

Les points clés:

* [Théorème «&nbsp;CAP&nbsp;»](https://en.wikipedia.org/wiki/CAP_theorem) - 
Selon ce théorème, «&nbsp;dans les cas ou une scission
du réseau a lieu, il faut choisir entre la cohérence et la disponibilité du
réseau, il est impossible d’avoir les deux&nbsp;». Cet argument intuitif est
simple&nbsp;: si le réseau se sépare en deux et que, dans le premier, je
réalise une transaction «&nbsp;envoie mes 10 pièces à A&nbsp;», puis que dans
le deuxième je transmets «&nbsp;envoie mes 10 pièces à B&nbsp;», alors soit le
système n’est plus disponible pour toutes les  transactions, puisqu’une des
transactions ou les deux n’obtiendront pas de réponses, soit il devient
incohérent puisqu’une des moitiés du réseau verra la première transaction
validée pendant que l’autre moitié du réseau verra la deuxième transaction
validée.  
* [Impossibilité «&nbsp;FLP&nbsp;»](http://the-paper-trail.org/blog/a-brief-tour-of-flp-impossibility/) - 
dans un environnement asynchrone (c’est à dire où il
n’y a pas de limites garanties à la latence du réseau, même entre des nœuds qui
fonctionnent correctement), il n’est pas possible de créer un algorithme qui
garantit d’atteindre un consensus dans une limite de temps définie s'il existe
ne serait-ce qu’un seul nœud défectueux/malhonnête. Cela n’invalide pas les
algorithmes dits 
«&nbsp;[Las Vegas](https://en.wikipedia.org/wiki/Las_Vegas_algorithm)&nbsp;»
qui ont une probabilité d’atteindre un consensus à chaque intervalle de temps
donné et vont donc atteindre ce consensus dans un intervalle de T secondes,
avec une probabilité exponentielle d’atteindre 1 à mesure que T augmente&nbsp;;
c’est en réalité la voie empruntée par l’essentiel des algorithmes de consensus
en usage.  
* Limites à la tolérance aux failles - de 
[l’article de DLS](http://groups.csail.mit.edu/tds/papers/Lynch/jacm88.pdf) 
nous retirons que&nbsp;: (i) les protocoles tournant dans un modèle
partiellement synchrone (c’est-à-dire qu’il y a une limite à la latence réseau
mais nous ne savons pas à l’avance laquelle) peuvent tolérer jusqu’à 1/3 de
défaillances quelconques («&nbsp;byzantines&nbsp;»), (ii) les protocoles
déterministes dans un modèle asynchrone (sans limite à la latence réseau) ne
tolèrent pas les défaillances (bien que leur article omette de mentionner que
[les algorithmes randomisés le puissent](http://link.springer.com/chapter/10.1007%2F978-3-540-77444-0_7), 
jusqu’à une tolérance de 1/3), (iii)
les protocoles dans un modèle synchrone (où la latence réseau est garantie
inférieure à une limite connue) peuvent, étonnamment, tolérer jusqu’à 100% de
défaillances), bien qu’il existe des restrictions quant à ce qui se produit
quand la moitié ou plus des nœuds sont défaillants. On note que le modèle
«&nbsp;byzantin authentifié&nbsp;» est celui qui vaut la peine d’être pris en
considération et non le simple «&nbsp;byzantin&nbsp;»&nbsp;;
«&nbsp;authentifié&nbsp;» signifie simplement que nous utilisons la
cryptographie à clefs publiques dans nos algorithmes, ce qui est de nos jours
un domaine bien connu et très peu coûteux.

La preuve de travail a été 
[rigoureusement analysée par Andrew Miller et d'autres](https://socrates1024.s3.amazonaws.com/consensus.pdf) 
et se définit dans cet article comme
un algorithme qui dépend d'un modèle de réseau synchrone. Bitcoin, par exemple,
possède une tolérance aux défaillances de 50% pour une latence réseau nulle, et
d'environ 49,5% dans les conditions d'observation réelles, mais qui descend à
33% si la latence réseau est égale au temps de bloc et se réduit à zéro lorsque
la latence réseau tend vers l'infini. Souvent, nous ne nous en rendons pas
compte parce que le temps de bloc de Bitcoin est très long (10 minutes) et,
même avec le temps de bloc d'Ethereum qui est de 14 secondes, la tolérance aux
défaillances reste d'environ 46%, mais aucune violation fondamentale de la
théorie de la tolérance aux défaillances byzantines ne se produit. Il faut
noter que la preuve de travail n’intègre pas le principe selon lequel, s’il 
y a N nodes dont le réseau permet 0,495 * N défaillances, les nœuds peuvent 
disposer d'une puissance différente les uns des autres. Cependant des 
abstractions ont été élaborées permettant de faire correspondre la puissance 
de calcul à un système théorique de tolérance aux défaillances de type byzantin.

Le consensus de preuve d’enjeu (_Proof-of-Stake_) est plus clairement un
problème de consensus tolérant aux défaillances de type byzantin&nbsp;; tout
comme en preuve de travail, la plupart des algorithmes peuvent atteindre une
tolérance aux défaillances de 50% en se fondant sur un modèle de réseau
synchrone. On connaît également des recherches, en particulier au sein du 
[projet Tendermint](https://github.com/tendermint/tendermint/wiki), 
sur
des algorithmes de consensus reposant sur des modèles de réseau synchrones ou
asynchrones. Tant la preuve de travail que la preuve d'enjeu ont choisi la
disponibilité plutôt que la cohérence, mais le mécanisme de finalité de Casper
ainsi que Tendermint ont choisi la cohérence.

Notons que la découverte du 
[_selfish mining_](https://bitcoinmagazine.com/articles/selfish-mining-a-25-attack-against-the-bitcoin-network-1383578440) 
ou minage égoïste par Ittay Eyal et Emin Gün Sirer, qui place des
limites de 25% et 33% sur la 
[compatibilité d’incitation](https://en.wikipedia.org/wiki/Incentive_compatibility) 
du minage de Bitcoin selon le modèle réseau (c’est-à-dire que
le minage n’est compatible avec des incitations que si des collusions
supérieures à 25% ou 33% sont impossibles), n’a RIEN à voir avec les résultats
de la recherche sur les algorithmes de consensus traditionnels, qui ne s’occupe
pas de compatibilité d’incitation.

### Qu’est-ce que le problème dit du «&nbsp;rien à perdre&nbsp;» (_nothing at stake_) et comment peut-il être résolu&nbsp;?

Dans de nombreux algorithmes de preuve d’enjeu anciens, y compris le Peercoin,
il n’existe que des récompenses à la production de blocs et pas de pénalités.
Ceci a pour conséquence fâcheuse que, dans le cas où de nombreuses chaînes
concurrente coexistent, le validateur a intérêt à créer des blocs pour chacune
de ces chaînes, juste pour s’assurer d’avoir créé le bloc pour la bonne
chaine&nbsp;:

<img class="aligncenter wp-image-1817 size-full"
src="https://www.ethereum-france.com/wp-content/uploads/2017/01/possec.png"
width="1324" height="509" />

En preuve de travail, cela obligerait le validateur a diviser par deux la
puissance de calcul qu’il attribue à chaque chaîne, ce qui ne serait pas
lucratif&nbsp;:

<img class="aligncenter size-full wp-image-1816"
src="https://www.ethereum-france.com/wp-content/uploads/2017/01/powsec.png"
alt="" width="1324" height="507" />

Le résultat en preuve d’enjeu est que si tous les acteurs sont purement
rationnels économiquement, même s’il n’existe aucun attaquant sur la
blockchain, il peut arriver qu’aucun consensus ne soit jamais trouvé. S’il y a
un attaquant, alors celui-ci doit seulement dominer les nœuds altruistes (qui
ne minent que la chaîne originale) et pas les nœuds rationnels (qui mineront
les deux chaînes «&nbsp;au cas où&nbsp;»), alors qu’en preuve de travail, où
l’attaquant doit surclasser les nœuds altruistes et les nœuds rationnels (ou du
moins représenter une menace crédible&nbsp;: voir les 
[attaques P + epsilon](https://blog.ethereum.org/2015/01/28/p-epsilon-attack/)). 
Certains prétendent que les participants sont incités à agir dans
les règles et à ne parier que sur la chaîne la plus longue afin de
«&nbsp;préserver la valeur de leur investissement&nbsp;», mais en oubliant que
cette incitation souffre du problème de la 
[tragédie des biens communs](https://fr.wikipedia.org/wiki/Trag%C3%A9die_des_biens_communs)&nbsp;: 
chaque détenteur d’avoir peut ne posséder qu’une
chance sur cent d’être «&nbsp;décideur&nbsp;» (c’est-à-dire en situation de
faire réussir ou échouer une attaque s’il y participe ou non), si le
dessous-de-table nécessaire à le convaincre personnellement de se joindre à une
attaque ne représente que 1% de la taille de son dépôt&nbsp;; la somme totale
nécessaire ne représenterait donc que 0,5 à 1% de la somme de tous les dépôts.
De plus, cet argument implique que toute situation de «&nbsp;_zéro chance de
défaillance_&nbsp;» n’est pas en équilibre stable car, si la probabilité de
défaillance est zéro, alors tout le monde a 0% de chance d’être décideur.

On peut résoudre ce problème par deux stratégies. La première, décrite en
termes généraux sous le nom «&nbsp;_Slasher_&nbsp;» 
[ici](https://blog.ethereum.org/2014/01/15/slasher-a-punitive-proof-of-stake-algorithm/)
et développée par Iddo Bentov 
[là,](https://arxiv.org/pdf/1406.5694.pdf) implique de pénaliser les
validateurs s’ils créent simultanément des blocs sur plusieurs chaînes en
intégrant une preuve de mauvaise conduite (c’est-à-dire deux en-têtes signés
entrant en conflit) dans la blockchain à un point dans l’avenir, déclenchant la
confiscation du dépôt du validateur malfaisant. Cela change la structure
d’incitations de cette manière&nbsp;:

<img class="aligncenter size-full wp-image-1815"
src="https://www.ethereum-france.com/wp-content/uploads/2017/01/slasher1sec.png"
alt="" width="1324" height="509" />

Pour que cet algorithme fonctionne, l’ensemble des validateurs doit être
déterminé très en avance dans le temps. Sinon, si un validateur dispose de 1%
des mises, et s’il existe deux branches A et B, alors 0,99% du temps le
validateur ne sera éligible que pour miser sur A et non sur B, 0,99% du temps
le validateur ne sera éligible que pour miser sur B et non sur A, et ce n’est
que 0,01% du temps que le validateur sera éligible pour miser sur les deux. Le
validateur peut donc miser double avec une efficience probabiliste de
99%&nbsp;: miser sur A si possible, miser sur B si possible, et, seulement si
le choix entre les deux est possible, miser sur la chaîne la plus longue. Cela
ne peut être évité que si la sélection des validateurs est la même pour tout
bloc sur les deux branches, ce qui demande que les validateurs soient
sélectionnés avant que la scission n’ait eu lieu (dans ces algorithmes, on
suppose que les fourches ne dureront pas plus que quelques heures, ce que l’on
obtient par une «&nbsp;limite de réversion&nbsp;» qui se contente d’interdire
aux nœuds d’accepter des fourches de longue durée&nbsp;; voir la section sur la
faible subjectivité plus loin). Cette solution comporte des inconvénients
propres, comme l’ouverture aux risques de collusion de validateurs à moyenne
portée (c’est-à-dire des situations où, par exemple, 25 sur 30 validateurs
successifs se regroupent et s’accordent à l’avance pour mettre en œuvre une
attaque à 51% sur les 19 blocs précédents) mais, si ces risques sont considérés
comme acceptables, elle fonctionne correctement.

La seconde stratégie consiste simplement à punir les validateurs qui créent des
blocs sur la _mauvaise _chaîne. C’est-à-dire que, s’il existe deux chaînes en
compétition, A et B, alors si un validateur crée un bloc sur N, il obtient une
récompense de +R sur B, mais l’en-tête du bloc peut être inclus dans A (ce qui
s’appelle un «&nbsp;dunkle&nbsp;» dans Casper) et sur A le validateur encourt
une pénalité de -F (éventuellement F = R). Cela change donc les calculs
économiques ainsi&nbsp;:

<img class="aligncenter size-full wp-image-1818"
src="https://www.ethereum-france.com/wp-content/uploads/2017/01/slasher2sec.png"
alt="" width="1324" height="511" />

Ici, l’intuition est que nous pouvons reproduire l’économie de la preuve de
travail au sein de la preuve d’enjeu. Dans la preuve de travail, il existe
aussi une pénalité pour la création de blocs sur la mauvaise chaîne, mais elle
est implicite dans l’environnement externe&nbsp;: les mineurs doivent dépenser
de l’électricité supplémentaire et acquérir ou louer du matériel
supplémentaire. Nous nous contentons de rendre ici les pénalités explicites. Ce
mécanisme a l’inconvénient d’imposer un risque légèrement supérieur sur les
validateurs (bien que l’effet s’atténue avec le temps) mais il a l’avantage de
ne pas nécessiter de connaître les validateurs à l’avance.

### Qu'est-ce que la «&nbsp;faible subjectivité&nbsp;»&nbsp;?

Il est important de noter que le mécanisme consistant à employer des dépôts /
mises de cryptomonnaies pour s'assurer qu'il y a «&nbsp;_quelque chose à
perdre_&nbsp;» mène à un changement dans le modèle de sécurisation. Supposons
que les dépôts soient bloqués pour un mois et puissent être ensuite retirés.
Supposons que se produise une tentative d'attaque à 51% qui revienne sur 10
jours de transactions. Les blocs créés par les attaquants peuvent tout
simplement être importés dans la chaîne principale comme preuve de malfaisance
(ils sont alors appelés «&nbsp;_dunkles_&nbsp;») et les validateurs peuvent
être punis. Maintenant, supposons qu'une attaque se produise après 40 jours.
Dans ce cas, même alors que les blocs peuvent être ré-importés, les validateurs
malfaisants seront à ce moment capables de retirer leurs dépôts sur la chaîne
principale et ne pourront donc pas être punis. Afin de résoudre ce problème,
une «&nbsp;limite d'annulation&nbsp;», c'est-à-dire une règle selon laquelle
les nœuds refusent simplement d'annuler plus loin dans le temps que la durée du
dépôt, est requise. Cela signifie alors que les nœuds doivent respecter deux
conditions supplémentaires&nbsp;:

1. Quand ils synchronisent la chaîne pour la première fois, ils doivent
authentifier le dernier état de manière externe. Ils peuvent s'y prendre en
consultant leurs amis, les explorateurs de blocs, etc. Notons que si leur node
ne trouve qu'une chaîne, alors il sait que cette unique chaîne est
correcte&nbsp;; ce n'est que dans le cas où deux chaînes existent, qui
divergent plus loin que la limite d'annulation, qu'une authentification sociale
est requise.
2. Les nœuds doivent se connecter au moins une fois à chaque limite
d'annulation. Si ce n'est pas le cas, une nouvelle étape d'authentification
sociale peut être requise.

Remarquons que cette authentification sociale est en fait d'une portée
extrêmement limitée&nbsp;; afin qu'elle devienne un vecteur d'attaque réel, un
attaquant devrait convaincre une très grande partie de la communauté que la
chaîne qu'il a créée est légitime alors qu'elle ne l'est pas, et même alors
l'attaquant ne pourrait convaincre que des nœuds nouvellement connectés.
Ceux-ci pourraient très bien ne recevoir qu'un point de contrôle récent avec le
logiciel&nbsp;; un attaquant qui peut corrompre le point de contrôle dans le
logiciel peut tout aussi bien corrompre le logiciel lui-même et aucune
vérification crypto-économique ne peut résoudre ce problème. Une fois un nœud
connecté, et tant qu'il se connecte suffisamment fréquemment, il peut rester en
phase avec la blockchain dans un modèle de sécurité purement crypto-économique
sans requérir d'authentification sociale supplémentaire.

De plus, on peut même automatiser au besoin l'authentification sociale de
plusieurs façons. L'une d'entre elles consiste à l'intégrer dans le workflow
naturel de l'utilisateur&nbsp;: une requête de paiement à la 
[BIP 70](https://github.com/bitcoin/bips/blob/master/bip-0070.mediawiki) 
pourrait inclure une empreinte de bloc récente et le logiciel client de
l'utilisateur s'assurerait qu'il se trouve sur la même chaîne que le
fournisseur avant d'approuver un paiement (ou d'ailleurs n'importe quelle
interaction sur la chaîne). Une autre consiste à utiliser le 
[_universal hash time_](https://www.youtube.com/watch?v=phXohYF0xGo) 
de
Jeff Coleman. Si l'UHT est utilisé, une chaîne d'attaque réussie doit être
générée secrètement en même temps que la chaîne légitime, exigeant ainsi qu'une
majorité de validateurs se concertent pendant toute cette période.

### Qu’est-ce que la «&nbsp;finalité économique&nbsp;»&nbsp;?

La finalité économique est un mécanisme visant à prévenir les _forks_ de
blockchain à moyen terme. Dans ce mécanisme, les validateurs s’engagent en
disant «&nbsp;_J’accepte de perdre Y dans tous les historiques ou le bloc B
n’est pas inclus dans la blockchain, en échange d’un gain de X dans tous les
historiques ou le bloc B y est inclus_&nbsp;». Lorsque les validateurs sont
suffisamment sûrs d’un bloc, ils seront prêts à miser des sommes très
importants sur Y, jusqu’au montant total de leur dépôt. Lorsqu’un bloc a
atteint un état tel qu’une très large portion des validateurs ont misé
l’ensemble de leur dépôt dessus, ce bloc est considéré comme «&nbsp;finalisé
économiquement&nbsp;». Cela permet d’assurer qu’une collusion de la majorité
des validateurs ne puisse mettre en œuvre une attaque à 51 % pour revenir sur
des transactions passées, sauf en supportant un coût extrêmement significatif.

### Peut-on pénaliser la censure dans un protocole de preuve d’enjeu&nbsp;?

Contrairement aux retours en arrière (_reverts_), la censure est très difficile
à prouver. La blockchain elle-même ne peut directement faire la différence
entre «&nbsp;l’utilisateur A a essayé d’envoyer la transaction X mais a été
censuré de façon injuste&nbsp;» et «&nbsp;l’utilisateur A a essayé d’envoyer la
transaction X mais elle n’a jamais été traitée car les frais de transaction
étaient insuffisants&nbsp;». Cependant, il existe un certain nombre de
techniques qui permettent de limiter les problèmes de censure.

La première est la résistance à la censure par le «&nbsp;problème de
l’arrêt&nbsp;» (_halting problem_). Dans la version la plus faible de cette
technique, le protocole est conçu pour être Turing-complet de façon qu’un
validateur ne puisse pas anticiper si une transaction va donner lieu à un
résultat non désiré sans dépenser une puissance de calcul significative en
exécutant la transaction avant de la traiter, et donc en s’exposant à des
attaques par déni de service (DoS). C’est la raison pour laquelle le 
[soft fork The DAO n’a pas pu être implémenté](http://hackingdistributed.com/2016/07/05/eth-is-more-resilient-to-censorship/).

Dans la version la plus forte de cette technique, les transactions peuvent
provoquer des «&nbsp;effets garantis&nbsp;» à un moment précis dans un avenir à
moyen terme. Par conséquent, un utilisateur pourrait envoyer de multiples
transactions qui interagissent l’une avec l’autre, avec inclusion
d’informations tierces déjà prévisibles qui conduiront à un événement futur, de
telle sorte que les validateurs ne peuvent pas prévoir comment les transactions
vont s’exécuter avant qu’elles ne soient insérées dans la blockchain (et
économiquement finalisées) et qu’il ne soit trop tard pour les arrêter&nbsp;;
même si toutes les transactions futures sont censurées, l’événement que les
validateurs voulaient éviter va tout de même avoir lieu. Dans ce schéma, les
validateurs pourraient toujours essayer d’empêcher toutes les transactions, ou
peut-être toutes les transactions qui ne comprennent pas une forme de preuve
formelle qu’elles ne conduisent pas à un résultat non désiré, mais cela
conduirait ces validateurs à refuser un trop grand nombre de transactions, au
point d’empêcher en pratique le système de fonctionner et donc de faire perdre
sa valeur à la blockchain et à sa crypto-monnaie. Les crypto-monnaies mis en
dépôt par les validateurs perdraient alors toute valeur.

La seconde méthode, 
[décrite ici par Adam Back](https://www.reddit.com/r/Bitcoin/comments/4j7pfj/adam_backs_clever_mechanism_to_prevent_miners/d34t9xa), 
est d’exiger que les transactions soient chiffrées par
un «&nbsp;verrou temporel&nbsp;» 
[timelock-encrypted]((https://www.gwern.net/Self-decrypting%20files)).
De fait, les validateurs incluront des transactions sans savoir leur contenu,
ledit contenu étant révélé plus tard automatiquement, à un stade ou il devient
beaucoup trop compliqué et coûteux d’exclure ces transactions. Si les
validateurs étaient suffisamment malfaisants, cependant, ils pourraient
simplement se mettre d’accord pour seulement accepter d’inclure les
transactions dotées d’une preuve cryptographique (par exemple zk-SNARK) de ce
que contient la version déchiffrée de la transactions, ce qui forcerait les
utilisateurs à télécharger un nouveau logiciel client mais ce logiciel pourrait
être facilement fourni par les censeurs et dans un modèle de théorie des jeux
les utilisateurs auraient intérêt à jouer le jeu.

Il est possible que la meilleure défense à envisager en preuve d’enjeu à ce
stade soit que les utilisateurs puissent toujours installer une mise à jour
logicielle incluant un hard fork supprimant les mauvais validateurs&nbsp;;
cette opération ne serait pas plus compliquée que celle décrite plus haut pour
rendre leurs transactions résistantes à la censure. Au final, cette méthode
serait modérément effective, mais présenterait le désavantage significatif de
ralentir la vitesse d’interaction avec la blockchain (il faut également noter
que cette méthode devrait être imposée à tous les utilisateurs pour être
effective; sinon des mauvais validateurs pourraient beaucoup plus simplement
filtrer les transactions chiffrées et censurer les transactions non-chiffrées).

Une troisième possibilité consiste à incorporer la détection de censure dans la
règle du choix de la branche. L'idée est simple. Les nœuds observent les
transactions sur le réseau et, s'ils en voient une avec des frais suffisants
pendant assez longtemps, ils assignent un «&nbsp;score&nbsp;» inférieur aux
chaînes qui ne l’incluent pas. Si tous les nœuds suivent cette stratégie, une
chaîne minoritaire intégrant ces transactions se constitue et tous les nœuds
honnêtes la suivent. La principale faiblesse de ce système est que les nœuds
hors ligne suivraient toujours la branche majoritaire et, en se reconnectant
après la fin d'une censure temporaire, ils se retrouveraient sur une branche
différente des nœuds en ligne. Ce système doit donc être vu plus comme
facilitant une coordination automatique d'urgence en cas de scission importante
que comme jouant un rôle actif dans le choix habituel de la branche.

### Comment fonctionne la sélection des validateurs et qu’est-ce que le pilonnage d'enjeu (_stake grinding_)&nbsp;?

Dans tout algorithme de preuve d’enjeu fondé sur une chaîne, il faut un
mécanisme qui sélectionne aléatoirement lequel des validateurs de l’ensemble
des validateurs actuellement actifs peut traiter le bloc suivant. Par exemple,
si l’ensemble des validateurs actuellement actifs est composé d’Alice avec 40
ether, Bob avec 30 ether, Charlie avec 20 ether et David avec 10 ether, il faut
qu’il existe 40% de chances pour qu’Alice soit la créatrice du prochain bloc,
30% de chances pour que ce soit Bob, etc. (en pratique, il ne faut pas
sélectionner aléatoirement un seul validateur mais une séquence infinie afin
que, si Alice manque à l’appel, quelqu’un d’autre puisse la remplacer après un
certain temps, mais cela ne change rien au problème de fond). Dans les
algorithmes ne reposant pas sur une chaîne, le hasard est souvent nécessaire
pour d’autres raisons.

Le «&nbsp;pilonnage d'enjeu&nbsp;» est une classe d’attaque où un validateur
effectue des calculs ou prend certaines mesures pour essayer de piper les dés
en sa faveur. Par exemple&nbsp;:

1. Dans [Peercoin](https://bitcointalk.org/index.php?topic=131901.0), 
un validateur pourrait «&nbsp;pilonner&nbsp;» de nombreuses combinaisons de
paramètres pour trouver ceux qui augmentent la probabilité que leur mise génère
un bloc valide.
2. Dans une implémentation aujourd’hui disparue, l’aléa pour le bloc N+1
dépendait de la signature du bloc N. Cela permettait à un validateur de
produire de nouvelles signatures de manière répétée jusqu’à trouver celle qui
leur permettait d’obtenir le bloc suivant, prenant ainsi perpétuellement le
contrôle du système.
3. Dans NXT, l’aléa pour le bloc N+1 dépend du validateur qui crée le bloc N.
Cela permet à un validateur de manipuler l’aléa en se contentant de sauter une
opportunité de créer un bloc. Il en découle un coût en opportunité égal à la
récompense du bloc mais, parfois, la nouvelle graine d’aléa (_random seed_)
peut donner au validateur un nombre de bloc supérieur à la moyenne pendant les
quelques dizaines de blocs suivants. Voir 
[ici](http://vitalik.ca/files/randomness.html) 
une analyse détaillée.

(1) et (2) sont faciles à résoudre&nbsp;; l’approche générale consiste à exiger
des validateurs qu’ils déposent leur mise très en avance et de ne pas utiliser
d’informations susceptibles d’être manipulées comme source de données
aléatoires. 

Il existe plusieurs stratégies principales pour résoudre les problèmes de type
(3). 

La première consiste à employer des systèmes fondés sur le 
[partage de secrets](https://en.wikipedia.org/wiki/Secret_sharing) 
ou les 
[signatures à seuils déterministes](https://eprint.iacr.org/2002/081.pdf) 
et de faire en sorte que les validateurs génèrent
collectivement la valeur aléatoire. Ces systèmes s’avèrent robustes contre
toute manipulation à moins qu’une majorité de validateurs se coalisent (mais,
dans certains cas et selon l’implémentation, entre 33% et 50% des validateurs
peuvent interférer avec l’opération, menant ainsi à une hypothèse d’activité de
67% pour le protocole).

La seconde consiste à utiliser des systèmes crypto-économiques où les
validateurs s’engagent sur une information (c’est-à-dire publier  sha3(x)) très
en avance, puis doivent publier x dans le bloc&nbsp;; x est ensuite ajouté au
groupement d’aléas. Il existe deux vecteurs d’attaque théoriques contre cette
stratégie&nbsp;:

1. Manipuler x au moment de l’engagement. C’est impraticable car le résultat
aléatoire prendrait en compte les valeurs de nombreux acteurs et, même si un
seul d’entre eux est honnête, la sortie donnera une distribution uniforme. Une
distribution uniforme XORée avec de nombreuses distributions faussées donne
toujours une distribution uniforme.
2. Éviter sélectivement de déployer des blocs. Cependant, cette attaque coûte
une récompense de bloc en coût d’opportunité et, comme le système empêche qui
que ce soit de voir les validateurs à venir sauf le suivant, elle ne fournit
presque jamais un revenu supérieur à une récompense de bloc. La seule exception
est le cas où, si un validateur saute, le validateur suivant ET le premier
enfant de ce validateur (celui qui doit valider le bloc suivant) sont
identiques&nbsp;; si cette situation s’avère dangereuse, il est aussi possible
de punir l’abandon de poste (le fait de ne pas générer de bloc à son tour) par
une pénalité de saut explicite.

La troisième consiste à utiliser la 
[«&nbsp;balise de majorité&nbsp;» d’Iddo Bentov](https://arxiv.org/pdf/1406.5694.pdf), 
qui génère un nombre aléatoire en prenant la majorité des
bits des N nombres aléatoires précédents générés par une autre balise
(c’est-à-dire que le premier bit du résultat est 1 si la majorité des premiers
bits des nombres sources est 1, et 0 sinon, le deuxième bit du résultat est 1
si la majorité des deuxièmes bits des nombres sources est 1, et 0 sinon, etc.).
Cela donne un coût d’exploitation de ~C * sqrt(N) où C est le coût
d’exploitation des balises sous-jacentes. 

Il en découle que, l’un dans l’autre, on connaît de nombreuses solutions au
pilonnage d'enjeu&nbsp;; le problème ressemble plus à la 
[cryptanalyse différentielle](https://fr.wikipedia.org/wiki/Cryptanalyse_diff%C3%A9rentielle) 
qu’au 
[problème de l’arrêt](https://fr.wikipedia.org/wiki/Probl%C3%A8me_de_l%27arr%C3%AAt), 
en représentant finalement une simple contrariété que les
concepteurs de la preuve de travail ont fini par comprendre et savent
maintenant résoudre, et non une faille fondamentale et insoluble.

### MC =&gt; MR ne signifie-t-il pas que tous les algorithmes de consensus avec un niveau de sécurité donné sont également efficients (ou également inefficients)&nbsp;?

Cet argument a été retenu par beaucoup et la meilleure description du problème
est peut-être celle de 
[Paul Sztorc dans cet article](http://www.truthcoin.info/blog/pow-cheapest/). 
Fondamentalement, si on crée le moyen de gagner 100$, les gens
vont vouloir dépenser jusqu’à 99.9$ (y compris le coût de leur propre travail)
afin de les obtenir&nbsp;; le coût marginal (MC) s’approche du revenu marginal
(MR). La théorie dit donc que tout algorithme avec une récompense de bloc
donnée sera également «&nbsp;inefficiente&nbsp;» en termes d’activité
socialement improductive effectuée afin d’obtenir la récompense.

Il y a trois failles dans ce raisonnement&nbsp;:

1. Il ne suffit pas de dire que le coût marginal s’approche du revenu
marginal&nbsp;; on doit également postuler un mécanisme plausible par lequel
quelqu’un peut réellement dépenser ce coût. Par exemple, si demain j’annonce
que chaque jour je donnerai 100$ à une personne sélectionnée aléatoirement dans
une liste de dix personnes (en utilisant le /dev/urandom de mon ordinateur
comme source de hasard), il n’y a tout simplement aucune manière possible pour
qui que ce soit de dépenser 99$ pour accéder à cet aléa. Soit on n’est pas dans
la liste des dix, auquel cas on n’a aucune chance quoi qu’on fasse, soit on est
dans la liste des dix et on n’a aucune manière abordable de manipuler ma source
de hasard, et on est bloqué sur la possibilité de recevoir 10$ par jour.
2. MC =&gt; MR n’implique PAS que le coût total s’approche du revenu total. Par
exemple, supposons qu’il existe un algorithme sélectionnant 1 000 validateurs
parmi un très grand ensemble (chaque validateur recevant une récompense de 1$),
que l’on ait 10% des mises donc qu’en moyenne on reçoive 100, et que pour un
coût de 1 $ on puisse forcer la remise à zéro de l’aléa (en pouvant le répéter
un nombre de fois illimité). En raison du 
[théorème central limite](https://fr.wikipedia.org/wiki/Th%C3%A9or%C3%A8me_central_limite), 
la déviation standard de la récompense est 10$ et, en se basant sur 
[d’autres résultats connus en mathématiques](http://math.stackexchange.com/questions/89030/expectation-of-the-maximum-of-gaussian-random-variables), 
le maximum attendu de N échantillons
aléatoires se trouve légèrement en dessous de M + S * sqrt(2 * log(N)) où M est
la moyenne et S la déviation standard. La récompense pour les tests
supplémentaires (en augmentant N) chute brutalement, c’est-à-dire que pour 0
tentative la récompense est de 100$, pour 1 tentative elle est de 105,5$, pour
deux elle est de 108,5$, pour trois elle est de 110,3 $, pour quatre elle est
de 111,6 $, pour cinq elle est de 112,6 $ et pour six de 113,5 $. Donc, après
cinq tentatives, cela n’en vaut plus la peine&nbsp;; un attaquant motivé
économiquement avec 10% des mises dépenserait de manière inefficace 5$ pour un
revenu supplémentaire de 13$, bien que le revenu total soit 113$. Si les
mécanismes exploitables n’exposent que de faibles opportunités, la perte
économique reste également faible&nbsp;; on ne peut donc PAS arguer de ce
qu’une simple goutte d’exploitabilité entraîne un gâchis économique du niveau
de la preuve de travail. Ce point sera tout aussi pertinent dans notre
discussion ultérieure sur les coûts de blocage des capitaux.
3. La preuve d’enjeu peut être sécurisée par des récompenses totales bien
inférieures à celles de la preuve de travail.

### Qu’en est-il des coûts de blocage des capitaux&nbsp;?

Le blocage de X ether dans un dépôt n’est pas gratuit&nbsp;; il entraîne le
sacrifice de possibilités pour le détenteur d’ether. Aujourd'hui, si j’ai 1 000
ether, je peux en faire ce que j’en veux&nbsp;; si je les bloque dans un dépôt,
ils y restent coincés pour des mois et je n’ai pas par exemple la possibilité
d'utiliser cet argent pour payer des dépenses inattendues. J’y perds également
en liberté de changer de support monétaire pendant cette période&nbsp;; je
pourrais simuler la vente d’ether en shortant (en vendant à découvert) un
montant équivalent au dépôt sur une bourse d’échange, mais cela engendre des
coûts dont les frais d’échange et les intérêts à payer. Certains pourraient
arguer de ce que cette inefficacité du blocage des capitaux serait en fait une
manière très indirecte d’aboutir à un niveau exactement identique
d’inefficacité économique que celui de la preuve de travail. La réponse est
non, pour les raisons (2) et (3) vues plus haut.

Commençons par (3). Considérons un modèle où les dépôts de preuve d’enjeu sont
à terme infini, les ASIC sont éternels, la technologie est fixée (plus de loi
de Moore) et les coûts d’électricité sont de zéro. Posons que le taux d’intérêt
à l’équilibre est de 5% par an. Dans une blockchain en preuve de travail, je
peux prendre 1000 $, les convertir en un mineur et le mineur me rapportera 50 $
en récompenses par an, pour toujours. Dans une blockchain en preuve d'enjeu, je
peux acheter pour 1000$ de monnaie, la mettre en dépôt (et les perdre pour
toujours) et obtenir 50 $ de récompense par an, pour toujours. Jusqu’ici, la
situation apparaît totalement symétrique (techniquement, même ici, dans la
preuve de travail, ma destruction n’est pas aussi complètement destructrice
d’un point de vue social car elle valorise les monnaies des autres, mais
mettons cela de côté pour l’instant). Le coût d’une attaque «&nbsp;ligne
Maginot&nbsp;» à 51% (acheter plus de ressources que le reste du réseau)
augmente de 1000 $ dans les deux cas.

Maintenant, effectuons les modifications suivantes à notre modèle&nbsp;:

1. La loi de Moore existe et les ASIC se déprécient de 50% tous les 2,772 ans
(cela correspond à un intérêt en composition continue de 25 % par an, choisi
pour simplifier les calculs). Si je veux obtenir le même effet «&nbsp;payer une
fois, recevoir de l’argent pour toujours&nbsp;», je peux faire ainsi&nbsp;: je
mettrais 1000 $ dans un fonds, où 167 $ iraient à un ASIC et les 833 $ restants
iraient dans des investissements à 5 % d’intérêt&nbsp;; les dividendes de 41,67
$ par an représenteraient juste assez pour renouveler le matériel ASIC (en
supposant un développement technologique totalement continu, encore une fois
pour simplifier les calculs). Les récompenses descendraient à 8,33 $ par
an&nbsp;; donc 83,3 % des mineurs abandonneraient avant que le système ne
revienne à l’équilibre et que je gagne 50 $ par an, et donc le coût
«&nbsp;ligne Maginot&nbsp;» d’une attaque sur preuve de travail avec les mêmes
récompenses chute d’un facteur de 6.
2. L’électricité plus la maintenance représentent 1/3 des coûts de minage. Ce
chiffre provient de statistiques récentes&nbsp;: l’un des nouveaux datacenters
de Bitfury consomme 
[0,06 joules par gigahash](http://www.coindesk.com/bitfury-details-100-million-georgia-data-center/), 
ou 60 J/TH ou 0.000017 kWh/TH, et, si nous supposons
une efficience similaire pour tout le réseau Bitcoin, nous obtenons 27,9 kWh
par seconde donnant une puissance de hachage totale de 
[1.67 million TH/s](http://bitcoinwatch.com/). 
L’électricité coûte 
[0,11$ par kWh](http://www.statista.com/statistics/477995/global-prices-of-electricity-by-select-country/) 
en Chine, ce qui fait environ 3$ par seconde ou 260000$ par jour.
Les récompenses de bloc de Bitcoin plus les frais font 600$ par BTC * 13 BTC
par bloc * 144 blocs par jour = 1,12 M$ par jour. L’électricité elle-même
représente 23% des coûts et nous pouvons estimer grosso modo les coûts de
maintenance à 10% pour obtenir une décomposition en 1/3 de coûts variables, 2/3
de coûts fixes. Cela signifie que du fonds de 1000$, seuls 111$ iraient à
l’ASIC, 55 $ aux coûts variables et 833 $ dans l’investissement en
matériel&nbsp;; le coût d’attaque «&nbsp;ligne Maginot&nbsp;» est donc 9 fois
inférieur à celui de la configuration d’origine.
3. Les dépôts sont temporaires et non permanents. Bien sûr, si je continue de
volontairement miser éternellement, cela ne change rien. Cependant, je regagne
un peu du choix dont je disposais auparavant&nbsp;; je peux abandonner à moyen
terme (disons 4 mois) quand je le désire. Cela signifie que je  voudrais y
mettre plus que 1000 $ en ether pour le gain de 50 $ par an&nbsp;; peut-être, à
l’équilibre, ce serait quelque chose comme 3000 $. Donc le coût de l’attaque
ligne Maginot en preuve d’enjeu _augmente _d’un facteur de trois et le PoS
donne 27 fois plus de sécurité, net, que le PoW pour le même coût.

Ce qui précède se base sur une modélisation très simplifiée, mais sert à
montrer comment de multiples facteurs se cumulent en faveur du PoS de telle
manière que le PoS en donne _plus_ pour son argent en termes de sécurité. Le
méta-argument selon lequel cet 
[argument multifactoriel peut-être suspect](http://lesswrong.com/lw/kpj/multiple_factor_explanations_should_not_appear/) 
fait tant pencher en faveur du PoS est
simple&nbsp;: en PoW, on travaille directement avec les lois de la physique. En
PoS, on peut concevoir le protocole de façon qu’il ait les propriétés
désirées&nbsp;; en bref, on peut _optimiser les lois de la physique en notre
faveur_. Le «&nbsp;piège caché&nbsp;» qui nous donne (3) est le changement du
modèle de sécurité, en particulier l’introduction de la faible subjectivité.

Nous pouvons maintenant parler de la distinction entre marginal et total, qui
est très importante dans le cas des coûts de blocage des capitaux. Par exemple,
considérons le cas où vous disposez de 100 000 $ en ether. Vous désirez
probablement en conserver une grande partie pendant longtemps&nbsp;; donc, même
le blocage de 50 000$ en ether devrait être quasiment gratuit. Le blocage de 80
000$ serait légèrement plus gênant mais 20 000$ de liquidités laisserait
toujours une belle marge de manœuvre. Le blocage de 90 000$ est plus ennuyeux,
99 000$ est très problématique et le blocage des 100 000$ serait absurde car
cela signifie que vous n’auriez pas le moindre bout d’ether pour payer les
frais de transaction de base. Nous pouvons montrer la différence entre cet état
des choses et l’état des chose en preuve de travail comme suit&nbsp;:

<img class="aligncenter size-full wp-image-1819"
src="https://www.ethereum-france.com/wp-content/uploads/2017/01/68747470733a2f2f626c6f672e657468657265756d2e6f72672f77702d636f6e74656e742f75706c6f6164732f323031342f30372f6c69717569646974792e706e67.png"
alt="" width="1561" height="736" />

Il s’ensuit que le coût _total_ de la preuve d’enjeu est potentiellement bien
inférieur au coût marginal du dépôt d’un ether supplémentaire dans le système
multiplié par le montant en ether actuellement en dépôt.

Notons que cette composante de l’argument ne se traduit malheureusement pas
pleinement en réduction du «&nbsp;_niveau sûr d’émission_&nbsp;». Cela nous
aide en montrant que nous pouvons obtenir une participation appréciable à la
preuve d’enjeu même si l’émission reste basse&nbsp;; cependant, cela signifie
également qu’une grande partie des gains ira simplement aux validateurs en tant
que surplus économique.

### Les plateformes d’échange en preuve d’enjeu engendreront-elles un risque de centralisation similaire aux coopératives en preuve de travail&nbsp;?

Du point de vue de la centralisation, tant pour Bitcoin que pour Ethereum, il
est avéré qu’à peu près trois _pools_ ou coopératives de minage sont
nécessaires pour coordonner une attaque à 51% (4 pour Bitcoin, 3 pour Ethereum
au moment d’écrire ces lignes). En PoS, si nous supposons une participation de
30% y compris tous les _exchanges_ ou bourses d’échange, alors il en suffirait
de trois pour lancer une attaque à 51%&nbsp;; si la participation monte à 40%,
alors le nombre requis passe à huit. Cependant, les bourses d’échange ne seront
pas en mesure de participer avec tout leur ether, car elles doivent honorer les
retraits.

De plus, la coopération en PoS est découragée en raison des exigences de
confiance bien plus élevées - une coopérative en preuve de travail peut
prétendre être hackée, détruire les dépôts de ses participants et demander une
récompense. D’un autre côté, la perspective de gagner des intérêts sur sa mise
sans faire tourner soi-même un nœud, même si la confiance est requise, peut en
attirer beaucoup&nbsp;; l’un dans l’autre, l’équilibre de la centralisation est
une question empirique pour laquelle la réponse n’est pas claire jusqu’à ce que
le système tourne pendant un certain temps. Avec le _sharding_ ou
fragmentation, nous nous attendons à ce que les incitations à la coopération
diminuent encore puisque (i) la variance représente encore moins un problème et
(ii) dans un modèle fragmenté, la charge de la vérification des transactions
est proportionnelle au montant de capital que l’on investit, et il n’y a donc
pas d’économies directes d’infrastructure à attendre d’une coopérative.

Enfin, la centralisation est moins nocive en preuve d’enjeu qu’en preuve de
travail, puisqu’il existe des manières moins coûteuses de se remettre
d’attaques à 51%&nbsp;; on n’a pas à changer d’algorithme de minage.

### Avec la preuve de travail, même si un cartel majoritaire venait à prendre temporairement le contrôle de la blockchain, il pourrait toujours être supplanté dans le futur par un encore plus gros cartel, alors que dans la preuve d’enjeu cela pourrait être impossible. Comment atténuer ce problème&nbsp;?

Premièrement, il est important de noter que, paradoxalement, l'aptitude à
supplanter un cartel en utilisant purement les aspects techniques du protocole
repose directement sur la sécurité du protocole, son architecture sous-jacente
important peu. Pour mieux comprendre, considérons que supplanter un cartel
impose de mettre en place encore plus (de puissance de minage | d'enjeu) que le
cartel précédent, puis de coordonner cette puissance afin de supplanter ce
cartel. Les pré-requis ici sont exactement les mêmes que les pré-requis pour
lancer une attaque à 51 %. Donc si ce fossé économique créé par la preuve de
travail est assez large pour lutter contre d'éventuelles attaques alors ce
fossé pourrait tout aussi bien se retourner contre les contre-attaques visant
l'attaquant.

Il y a en fait tout un champ des possibles entre ce que l’on peut appeler les
modèles de sécurité ouverts et les modèles de sécurité fermés. Les modèles de
sécurité ouverts sont plus faciles à attaquer de l’extérieur mais permettent
aussi des contre-attaques, et les modèles de sécurité fermés sont difficiles à
attaquer mais il y est également dur de déloger des attaquant ayant réussi,
sinon par un _hard fork_. Plusieurs algorithmes de preuve de travail s’alignent
au long de ce spectre&nbsp;: par exemple, les blockchains minées par CPU sont
plus ouvertes que les blockchains minées par ASIC car, si une blockchain minée
par CPU se fait attaquer, une capacité énorme peut être mobilisée à la
rescousse, alors que les blockchains minées par ASIC n’ont pas cette propriété
mais ont celle d’exiger d’une attaque une dépense en capital considérable.

Notons en particulier qu’il est possible de créer un protocole où la preuve
d’une attaque en cours peut être utilisée pour augmenter temporairement la
récompense de minage (disons de 50%) et pour mettre en pause les ajustements de
difficulté&nbsp;; ce faisant, et en supposant que la puissance de hachage peut
facilement flotter entre différentes blockchains minées par CPU, on peut alors
garantir que toute attaque fera l’objet d’une riposte avec la puissance de 33%
de _toutes_ les blockchains minées par CPU, ensemble (ceci parce qu’à
l’équilibre toutes les chaînes sont également profitables et, en conditions
d’urgence, la chaîne attaquée sera temporairement 50% plus profitable en
drainant la puissance de hachage des autres chaînes jusqu’à ce que leur
difficulté chute de 33% et qu’un nouvel équilibre temporaire soit atteint). Si
le marché de la puissance de calcul était assez liquide, même la capacité libre
de systèmes comme AWS pourrait être exploitée.

On pourrait même imaginer un système hybride PoW/PoS où de grandes quantités de
puissance de calcul pourraient faire échec à des chaînes en cours de
finalisation économique, où la puissance de calcul serait récompensée par une
partie des pénalités payées sur les dépôts du validateur de la chaîne
d’attaque&nbsp;; l’utilisation des pénalités à payer pour exploiter une
quantité quasiment illimitée de puissance de calcul globale servirait de
«&nbsp;dissuasion nucléaire&nbsp;» pour empêcher même les simples tentatives
d’attaque.

Casper peut trouver sa place en de nombreux endroits sur ce spectre selon le
pourcentage de puissance d’enjeu. Si 50% de tous les enjeux participent, cela
devient un modèle de sécurité purement fermé. Si moins de 10% des enjeux
participent, alors il existe un marché très liquide duquel n’importe qui peut
acheter de l’ether pour devenir un «&nbsp;miseur&nbsp;» (_staker_), et le
modèle de sécurité devient donc plus ouvert (bien que pour y arriver, il faille
utiliser une «&nbsp;règle du choix de la branche active&nbsp;»). Si une
«&nbsp;ouverture&nbsp;» encore plus grande était désirée, alors la
«&nbsp;preuve de travail hybride pour dissuasion nucléaire&nbsp;» semble le
meilleur choix.

<small>Document d'origine : Vitalik Buterin - [Proof of Stake FAQ](https://github.com/ethereum/wiki/wiki/Proof-of-Stake-FAQ)</small>

<small>Traduction : Jean Zundel, Simon Polrot et Alexis Masseron - [FAQ - Proof of Stake](https://github.com/asseth/docs-traductions/blob/master/faq/PoS-FAQ-fr.md), originellement publiée sur [Ethereum France](https://www.ethereum-france.com/quest-ce-que-la-preuve-denjeu-proof-of-stake-faq-par-v-buterin-traduction-francaise/) - [Qu'est-ce que la preuve d'enjeu ou _Proof of Stake_&nbsp;?](https://github.com/asseth/docs-traductions/blob/master/faq/PoS-FAQ-fr.md)
