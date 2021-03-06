# Marcus - Circuit standard des boucliers

## 1. Améliorations pour la prochaine version

1. Ajouter des points de tests pour le raccordement facile d'un oscilloscope;
2. Ajouter un bornier de programmation pour le PIC avec le PICkit2;
3. Inverser la masse et le 5V sur le bouton de réinitialisation, et s'assurer que tous les signaux sont raccordés;
4. Inverser les interrupteurs DIP pour la programmation des points de vie;
5. À partir de maintenant, toujours indiquer les alimentations sur la couche de texte. Sur les autres borniers, indiquer la borne 1 et les masses.

### 1.1 Améliorations optionnelles, à considérer

1. Peut-être ajouter une LED verte pour indiquer que le robot est encore en vie, quitte à mettre un mini-bornier pour un cavalier. Mais c'est vraiment optionnel, à valider;
2. Remplacer les sorties digitales non-essentielles (information) par un port de communication I2C;
3. Faire un format de PCB pouvant être empilé avec le BBB.

## 2. Caractéristiques techniques

- Tension d'alimentation
  - Minimal : 5Vcc
  - Maximal : 6Vcc
- Niveau logique
  - 0-5 Vcc (TTL)
- Temps de réaction
  - À compléter
- Fréquence : 4MHz

## 3. Borniers d'interface

- TB0 : Alimentation
  - 1. Gnd
  - 2. 5V
- TB1 : Interface contrôleur
  - 1. Mort
  - 2. Bouclier 1
  - 3. Bouclier 2
  - 4. Bouclier 3
  - 5. Bouclier 4
  - 6. Réinitialise
  - 7. RB7
  - 8. Gnd
- TB2 : Interface boucliers
  - 1. Bouclier 1
  - 2. Gnd
  - 3. Bouclier 2
  - 4. Gnd
  - 5. Bouclier 3
  - 6. Gnd
  - 7. Bouclier 4
  - 8. Gnd

### 3.1 TB0 – Alimentation

Ce bornier sert à fournir une alimentation régulée à l'externe à une tension entre 5 et 6 VCC. Il n'y a aucun régulateur de tension ni condensateur régulateur dans le circuit standard des boucliers. La masse doit être isolée des composants de puissance afin d'éviter de perturber le fonctionnement des composants sensibles du circuit.

### 3.2 TB1 – Interface contrôleur

L'interface contrôleur sert à communiquer de l'information sur l'état des boucliers au contrôleur principal, et à recevoir des commandes de réinitialisation une fois que les données ont bien été communiquées.

#### 3.2.1 Sorties

Les bornes 3 à 7, c'est-à-dire « Mort », « Bouclier 1 », « Bouclier 2 », « Bouclier 3 » et « Bouclier 4 » servent à communiquer l'état des boucliers, ainsi que le statut de mort (voir section 4). La sortie « Mort » est normalement à 0 et passe à 1 (voir section 2) lorsque les points de vies ont été épuisés (voir section 4). Après que la sortie soit passée à 1, une réinitialisation manuelle […] ou une coupure de l'alimentation V+ est requise afin de remettre le compteur de vies à sa valeur initiale et remettre la sortie de mort à 0. Les sorties « Bouclier 1 », « Bouclier 2 », « Bouclier 3 » et « Bouclier 4 » sont normalement à 0 et passent à 1 (voir section 2) lorsqu'un impact réussi […] est détecté sur le bouclier correspondant. La sortie en question est maintenue à 1 jusqu'à la prochaine réinitialisation logicielle (voir section 3.2.2). Il est important de noter que la réinitialisation des sorties 4 à 7 n'a aucun effet sur le décompte des impacts, et donc le statut de mort.

#### 3.2.2 Entrée

La borne 8 « Réinitialisation » sert à ramener l'état des sorties 4 à 7 (voir section 3.2.1) à 0, soit leur état au repos. Elle est activée par un 1 logique et n'a aucun effet sur le décompte des impacts ni le statut de mort. Elle est à la disposition du concepteur, pour information, dans le cas où il voudrait inclure l'état des boucliers à sa logique.

#### 3.2.3 RB7

La borne RB7 est actuellement non-utilisée, mais raccordée au signal RB7 du PIC.

### 3.3 TB2 – Interface boucliers

Raccordement des capteurs LDT0 de MEAS (numéro de matériel 0-1002794-1, voir note d'application 111398). Le LM324N alimenté à 0-5V agit comme un redresseur simple alternance, avec un filtre passe-bas ayant une fréquence de coupure de 1/(2*pi*C*R2) = 8Hz, et un gain 1+R2/R1 = 21 à 0Hz. Le signal résultant vers le PIC est donc une belle courbe de détection lissée. Il reste à tester la réponse sur le montage final et à ajuster le gain, au besoin.

Le LDT0 devrait être utilisé comme capteur de vibration, il est donc possible que le gain doive être augmenté considérablement lorsqu'il sera monté sur son bouclier. Une autre solution serait de faire un bouclier flexible et léger et de le fixer sur le capteur lui-même.

En résumé, il reste des tests à faire mais il faudra simplement ajuster les 2 résistances et le condensateur de chaque circuit.

## 4. Points de vie

Les points de vie, à l'état initial, sont définis par la configuration d'un interrupteur DIP à 6 positions alors que le circuit est non-alimenté. Chaque impact détecté abaisse la quantité de points de vie de 1, jusqu'à ce qu'elle atteigne 0. La borne 3 « Mort » passe alors à 1 et le circuit doit être réinitialisé par la méthode décrite en 3.2.1.
