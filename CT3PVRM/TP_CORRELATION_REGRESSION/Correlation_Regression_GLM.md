# TP : Corrélation et Régression linéaire en aquaculture
## Étude de la croissance et des paramètres environnementaux

---

## Contexte biologique

Une station de recherche aquacole étudie les facteurs influençant la croissance de la **daurade royale** (*Sparus aurata*), un poisson d'élevage méditerranéen à forte valeur commerciale. Plusieurs expériences ont été menées pour comprendre les relations entre :

- La **température de l'eau** et le taux de croissance
- La **concentration en oxygène dissous** et la croissance
- Le **poids** et la **longueur** des poissons
- La **densité de chlorophylle** (nourriture naturelle) et la croissance du plancton

Ce TP vous permettra de :
1. Comprendre et calculer des **corrélations de Pearson**
2. Différencier **corrélation** et **régression linéaire**
3. Construire et interpréter des **modèles de régression linéaire simple et multiple**
4. Introduire les **Modèles Linéaires Généralisés (GLM)** pour des données de comptage

---

## Préparation de l'environnement de travail

### Installation et chargement des packages

```r
# Installation des packages (si nécessaire)
install.packages(c("ggplot2", "dplyr", "corrplot", "car", "lmtest", "ggpubr"))

# Chargement des packages
library(ggplot2)    # Pour les graphiques
library(dplyr)      # Pour la manipulation de données
library(corrplot)   # Pour les matrices de corrélation
library(car)        # Pour les diagnostics de régression
library(lmtest)     # Pour les tests sur les modèles linéaires
library(ggpubr)     # Pour les graphiques avec statistiques
```

### Chargement des données

Les données se trouvent dans le fichier **`donnees_aquaculture.xlsx`** qui contient **2 feuilles** :
- **Feuille 1 : `daurade`** - Données de croissance de la daurade royale
- **Feuille 2 : `plancton`** - Données d'abondance planctonique

#### Méthode 1 : Charger depuis un fichier Excel (.xlsx)

```r
# Définir le répertoire de travail
setwd("chemin/vers/votre/dossier")

# Installer et charger le package readxl (si nécessaire)
install.packages("readxl")
library(readxl)

# Charger les données depuis les différentes feuilles du fichier Excel

# Visualiser la structure des données
str(daurade)
head(daurade)

str(plancton)
head(plancton)
```

---

**Description des jeux de données** :

**Feuille 1 : `daurade`**
- **80 observations** de daurades royales (*Sparus aurata*)
- **Variables** :
  - `temperature` : Température de l'eau (°C)
  - `oxygene` : Concentration en oxygène dissous (mg/L)
  - `poids` : Poids du poisson (grammes)
  - `longueur` : Longueur totale du poisson (cm)
  - `taux_croissance` : Taux de croissance journalier (g/jour)

**Feuille 2 : `plancton`**
- **60 observations** d'échantillons planctoniques
- **Variables** :
  - `temperature` : Température de l'eau (°C)
  - `chlorophylle` : Concentration en chlorophylle-a (µg/L)
  - `abondance` : Nombre d'organismes planctoniques comptés (entier)

---

## PARTIE 1 : Corrélation de Pearson (45 min)

### 1.1 Rappel théorique

Le **coefficient de corrélation de Pearson** (noté *r*) mesure la **force et la direction** d'une relation **linéaire** entre deux variables quantitatives continues.

**Propriétés** :
- **Valeurs** : r ∈ [-1, +1]
- **r = +1** : corrélation positive parfaite (relation linéaire croissante)
- **r = -1** : corrélation négative parfaite (relation linéaire décroissante)
- **r = 0** : absence de corrélation linéaire (attention : il peut y avoir une relation non-linéaire !)

**Interprétation de l'intensité** :
- |r| < 0.3 : corrélation faible
- 0.3 ≤ |r| < 0.7 : corrélation modérée
- |r| ≥ 0.7 : corrélation forte

**Hypothèses du test** :
- H₀ : ρ = 0 (pas de corrélation linéaire dans la population)
- H₁ : ρ ≠ 0 (il existe une corrélation linéaire)

**Conditions d'application** :
1. Variables **quantitatives continues**
2. Relation **linéaire** entre les variables
3. Absence de **valeurs extrêmes** (outliers) influentes
4. Pour le test de significativité : **normalité bivariée** (souvent relaxée pour n > 30)

### 1.2 Exploration des données

**Question 1.1** : Affichez la structure du jeu de données `daurade` et vérifiez qu'il contient les variables suivantes : `temperature`, `oxygene`, `poids`, `longueur`, `taux_croissance`.

```r
# Votre code ici
```

**Question 1.2** : Calculez les statistiques descriptives (moyenne, écart-type, min, max) pour chaque variable.

```r
# Votre code ici
# Indice : utilisez summary() et sapply()
```

**Question 1.3** : Créez un nuage de points (*scatterplot*) pour visualiser la relation entre la **température** et le **taux de croissance**.

```r
# Votre code ici avec ggplot2
```

**Question 1.4** : Que pouvez-vous dire visuellement sur la relation entre ces deux variables ? La relation semble-t-elle linéaire ? Positive ou négative ?

**Réponse :**

### 1.3 Calcul de la corrélation de Pearson

**Question 1.5** : Calculez le coefficient de corrélation de Pearson entre la température et le taux de croissance.

```r
# Votre code ici
# Utilisez cor()
```

**Question 1.6** : Testez la significativité de cette corrélation avec `cor.test()`.

```r
# Votre code ici
```

Complétez le tableau suivant :

| Statistique | Valeur |
|-------------|--------|
| Coefficient r |        |
| p-value |        |
| Intervalle de confiance 95% |        |
| Conclusion (α = 0.05) |        |

**Question 1.7** : Interprétez ce résultat en termes biologiques. La température influence-t-elle la croissance de la daurade ?

**Réponse :**

### 1.4 Matrice de corrélation

Lorsqu'on étudie plusieurs variables simultanément, il est utile de calculer toutes les corrélations deux à deux.

**Question 1.8** : Calculez la matrice de corrélation pour toutes les variables numériques de `daurade`.

```r
# Sélectionner uniquement les variables numériques
variables_numeriques <- daurade %>%
  select(temperature, oxygene, poids, longueur, taux_croissance)

# Calculer la matrice de corrélation
matrice_cor <- cor(variables_numeriques)

# Afficher la matrice
round(matrice_cor, 3)
```

**Question 1.9** : Créez une visualisation graphique de cette matrice de corrélation.

```r
# Votre code ici
library(corrplot)
corrplot(matrice_cor, method = "circle", type = "upper",
         addCoef.col = "black", number.cex = 0.7,
         tl.col = "black", tl.srt = 45,
         title = "Matrice de corrélation - Daurade royale")
```

**Question 1.10** : Identifiez dans la matrice :
- La corrélation la plus forte (en valeur absolue)
- La corrélation la plus faible


**Réponse :**

### 1.5 Corrélation ≠ Causalité

**Question 1.11** : Calculez la corrélation entre le **poids** et la **longueur** des daurades.

```r
# Votre code ici
```

Cette corrélation est très probablement élevée et significative.

**Question 1.12** : Discutez la phrase suivante :
*"Une forte corrélation entre deux variables prouve qu'une variable cause l'autre."*

Est-ce vrai dans le cas poids-longueur ? Pourquoi ?

**Réponse :**

**💡 IMPORTANT À RETENIR** :
- **Corrélation ≠ Causalité**
- Une forte corrélation peut indiquer :
  1. A cause B
  2. B cause A
  3. A et B sont influencés par une variable C (confondante)
  4. Coïncidence / Hasard
- Seules des **expériences contrôlées** peuvent démontrer la causalité

---

## PARTIE 2 : Régression linéaire simple 

### 2.1 Rappel théorique

La **régression linéaire** modélise la relation entre une variable **dépendante** (Y, réponse) et une ou plusieurs variables **indépendantes** (X, explicatives).

**Modèle de régression linéaire simple** :
```
Y = β₀ + β₁X + ε
```

Où :
- **Y** : variable dépendante (à expliquer)
- **X** : variable indépendante (explicative)
- **β₀** : ordonnée à l'origine (*intercept*)
- **β₁** : pente (coefficient de régression)
- **ε** : erreur (résidu)

**Différences entre corrélation et régression** :

| Aspect | Corrélation | Régression |
|--------|-------------|------------|
| **Objectif** | Mesurer la force d'association | Prédire Y à partir de X |
| **Symétrie** | Symétrique (cor(X,Y) = cor(Y,X)) | Asymétrique (Y dépend de X) |
| **Variables** | Deux variables équivalentes | Variable dépendante vs indépendante |
| **Résultat** | Coefficient r (-1 à +1) | Équation de prédiction |
| **Prédiction** | Non | Oui |

**Question 2.1** : En vos propres mots, expliquez la différence principale entre corrélation et régression.

**Réponse :**

### 2.2 Construction du modèle de régression

Nous allons modéliser le **taux de croissance** (Y) en fonction de la **température** (X).

**Question 2.2** : Avant de construire le modèle, répondez aux questions suivantes :
- Quelle est la variable dépendante (Y) ?
- Quelle est la variable indépendante (X) ?
- Pourquoi ce choix est-il logique biologiquement ?

**Réponse :**

**Question 2.3** : Construisez le modèle de régression linéaire.

```r
# Votre code ici
modele1 <- lm(taux_croissance ~ temperature, data = daurade)

# Afficher le résumé du modèle
summary(modele1)
```

**Question 2.4** : Complétez le tableau suivant à partir des résultats :

| Paramètre | Estimation | Erreur standard | t-value | p-value |
|-----------|------------|-----------------|---------|---------|
| β₀ (Intercept) |        |                 |         |         |
| β₁ (température) |        |                 |         |         |

**Autres statistiques du modèle** :

| Statistique | Valeur |
|-------------|--------|
| R² (coefficient de détermination) |        |
| R² ajusté |        |
| F-statistic |        |
| p-value du modèle |        |

**Question 2.5** : Écrivez l'équation de la droite de régression.

```
Taux de croissance = ________ + ________ × Température
```

**Question 2.6** : Interprétez la pente (β₁) en termes biologiques.

**Réponse :**

**Question 2.7** : Interprétez le R² (coefficient de détermination).

**Rappel** : R² représente la proportion de variance de Y expliquée par X.

**Réponse :**

### 2.3 Visualisation du modèle

**Question 2.8** : Créez un graphique montrant les données et la droite de régression.

```r
# Méthode 1 : avec ggplot2 (simple)
ggplot(daurade, aes(x = temperature, y = taux_croissance)) +
  geom_point(color = "steelblue", size = 3, alpha = 0.6) +
  geom_smooth(method = "lm", se = TRUE, color = "red", fill = "pink") +
  labs(title = "Régression linéaire : Croissance vs Température",
       subtitle = paste("R² =", round(summary(modele1)$r.squared, 3)),
       x = "Température (°C)",
       y = "Taux de croissance (g/jour)") +
  theme_minimal()

# Méthode 2 : avec stat_regline_equation de ggpubr (avec équation)
library(ggpubr)
ggplot(daurade, aes(x = temperature, y = taux_croissance)) +
  geom_point(color = "steelblue", size = 3, alpha = 0.6) +
  geom_smooth(method = "lm", se = TRUE, color = "red", fill = "pink") +
  stat_regline_equation(label.y = max(daurade$taux_croissance) * 0.95,
                        aes(label = ..eq.label..)) +
  stat_regline_equation(label.y = max(daurade$taux_croissance) * 0.90,
                        aes(label = ..rr.label..)) +
  labs(title = "Régression linéaire : Croissance vs Température",
       x = "Température (°C)",
       y = "Taux de croissance (g/jour)") +
  theme_minimal()
```

### 2.4 Vérification des hypothèses de la régression

Pour qu'un modèle de régression linéaire soit valide, plusieurs hypothèses doivent être respectées :

1. **Linéarité** : la relation entre X et Y est linéaire
2. **Indépendance** : les observations sont indépendantes
3. **Normalité des résidus** : les résidus suivent une loi normale
4. **Homoscédasticité** : variance constante des résidus
5. **Absence de valeurs aberrantes** influentes

**Question 2.9** : Créez les graphiques de diagnostic automatiques.

```r
# Votre code ici
par(mfrow = c(2, 2))
plot(modele1)
par(mfrow = c(1, 1))
```

**Interprétation des 4 graphiques** :

1. **Residuals vs Fitted** : Vérifier la linéarité et l'homoscédasticité
   - Attendu : points dispersés aléatoirement autour de 0

2. **Normal Q-Q** : Vérifier la normalité des résidus
   - Attendu : points alignés sur la diagonale

3. **Scale-Location** : Vérifier l'homoscédasticité
   - Attendu : ligne horizontale

4. **Residuals vs Leverage** : Identifier les valeurs influentes
   - Attendu : pas de points au-delà de la distance de Cook

**Question 2.10** : Testez formellement la normalité des résidus avec le test de Shapiro-Wilk.

```r
# Votre code ici
shapiro.test(residuals(modele1))
```

Interprétation (p > 0.05 = normalité respectée) :

**Question 2.11** : Testez l'homoscédasticité avec le test de Breusch-Pagan.

```r
# Votre code ici
library(lmtest)
bptest(modele1)
```

---

**📘 FOCUS : Pourquoi le test de Breusch-Pagan ?**

Le test de **Breusch-Pagan** teste l'**homoscédasticité** : la variance des résidus doit être constante le long de la droite de régression.

**Pourquoi pas le test de Bartlett ?**

| Test | Usage | Contexte |
|------|-------|----------|
| **Bartlett** | Compare les variances de **plusieurs groupes fixes** | ANOVA : "Les régimes A, B, C ont-ils la même variance ?" |
| **Breusch-Pagan** | Teste si la variance change le long d'une **variable continue** | Régression : "La variance change-t-elle quand température augmente ?" |

**En régression linéaire** :
- On a une **variable continue** (température : 15°C, 16°C, 17°C...), pas des groupes fixes
- On veut savoir si la variance des résidus **augmente ou diminue** avec X
- Le test de Breusch-Pagan régresse les résidus² sur X pour détecter cette relation

**Exemple concret** :
```r
# Si variance augmente avec la température :
# Petits poissons (15°C) : résidus entre -0.5 et +0.5 (variance faible)
# Gros poissons (28°C) : résidus entre -2.0 et +2.0 (variance élevée)
# → Hétéroscédasticité → Breusch-Pagan significatif (p < 0.05)
```

**Hypothèses du test** :
- **H₀** : Variance constante (homoscédasticité) ✓
- **H₁** : Variance dépend de X (hétéroscédasticité) ✗

**Interprétation** :
- **p > 0.05** → Homoscédasticité respectée ✓
- **p < 0.05** → Hétéroscédasticité détectée → Transformer les données (log) ou utiliser régression robuste

---

Interprétation (p > 0.05 = homoscédasticité respectée) :

**Question 2.12** : Les hypothèses du modèle sont-elles respectées ? Le modèle est-il fiable ?

**Réponse :**

### 2.5 Prédictions avec le modèle

Une fois le modèle validé, on peut l'utiliser pour faire des prédictions.

**Question 2.13** : Utilisez le modèle pour prédire le taux de croissance à des températures de 18°C, 22°C et 26°C.

```r
# Créer un dataframe avec les nouvelles valeurs
nouvelles_temp <- data.frame(temperature = c(18, 22, 26))

# Faire les prédictions
predictions <- predict(modele1, newdata = nouvelles_temp, interval = "confidence")

# Afficher les résultats
cbind(nouvelles_temp, predictions)
```

**Question 2.14** : Complétez le tableau :

| Température (°C) | Taux de croissance prédit | Intervalle de confiance 95% |
|------------------|---------------------------|------------------------------|
| 18 |  |  |
| 22 |  |  |
| 26 |  |  |

**Question 2.15** : Peut-on utiliser ce modèle pour prédire le taux de croissance à 35°C ? Justifiez votre réponse.

**Réponse :**

**💡 Attention à l'extrapolation** : Ne jamais utiliser un modèle en dehors de la plage des données observées !

---

## PARTIE 3 : Régression linéaire multiple

### 3.1 Principe

La **régression multiple** permet de modéliser Y en fonction de **plusieurs** variables explicatives X₁, X₂, ..., Xₚ.

**Modèle** :
```
Y = β₀ + β₁X₁ + β₂X₂ + ... + βₚXₚ + ε
```

**Avantages** :
- Prendre en compte plusieurs facteurs simultanément
- Mieux expliquer la variabilité de Y (R² plus élevé)
- Contrôler l'effet des variables confondantes

**Question 3.1** : Nous allons modéliser le taux de croissance en fonction de la **température** ET de l'**oxygène dissous**. Pourquoi est-il pertinent d'inclure ces deux variables en aquaculture ?

**Réponse :**

### 3.2 Construction du modèle multiple

**Question 3.2** : Construisez un modèle de régression multiple avec température et oxygène comme prédicteurs.

```r
# Votre code ici
modele2 <- lm(taux_croissance ~ temperature + oxygene, data = daurade)
summary(modele2)
```

**Question 3.3** : Complétez le tableau des coefficients :

| Paramètre | Estimation | Erreur standard | t-value | p-value | Significatif ? |
|-----------|------------|-----------------|---------|---------|----------------|
| β₀ (Intercept) |        |                 |         |         |                |
| β₁ (température) |        |                 |         |         |                |
| β₂ (oxygène) |        |                 |         |         |                |

**Statistiques du modèle** :

| Statistique | Modèle simple (temp seule) | Modèle multiple (temp + O₂) |
|-------------|----------------------------|------------------------------|
| R² |  |  |
| R² ajusté |  |  |
| F-statistic |  |  |

**Question 3.4** : Comparez les deux modèles. Le modèle multiple explique-t-il mieux la variabilité du taux de croissance ? Justifiez.

**Réponse :**

**Question 3.5** : Écrivez l'équation de prédiction du modèle multiple.

```
Taux de croissance = ________ + ________ × Température + ________ × Oxygène
```

**Question 3.6** : Interprétez le coefficient de l'oxygène (β₂).

**Réponse :**

### 3.3 Vérification des hypothèses

**Question 3.7** : Créez les graphiques de diagnostic pour le modèle multiple.

```r
# Votre code ici
par(mfrow = c(2, 2))
plot(modele2)
par(mfrow = c(1, 1))
```

**Question 3.8** : Vérifiez la normalité et l'homoscédasticité.

```r
# Normalité
shapiro.test(residuals(modele2))

# Homoscédasticité
bptest(modele2)
```

**Question 3.9** : Testez la **multicolinéarité** avec le VIF (Variance Inflation Factor).

```r
# Votre code ici
library(car)
vif(modele2)
```

---

**📘 FOCUS : Qu'est-ce que la multicolinéarité et le VIF ?**

### Définition de la multicolinéarité

La **multicolinéarité** survient lorsque **deux ou plusieurs variables explicatives** d'un modèle de régression multiple sont **fortement corrélées entre elles**.

**Exemple** : Dans notre modèle `taux_croissance ~ température + oxygène`
- Si température et oxygène sont très corrélés (r = -0.90 par exemple)
- Le modèle ne peut pas distinguer clairement l'effet propre de chaque variable
- Les estimations des coefficients deviennent **instables**

### Pourquoi c'est un problème ?

Quand il y a multicolinéarité forte :

1. **Coefficients instables** : De petits changements dans les données entraînent de grandes variations des coefficients
2. **Erreurs standard gonflées** : Les erreurs standard des coefficients augmentent → intervalles de confiance très larges
3. **Tests non significatifs** : Les variables peuvent sembler non significatives alors qu'elles le sont individuellement
4. **Interprétation difficile** : Impossible de séparer l'effet propre de chaque variable
5. **Prédictions OK** : Les prédictions restent correctes, mais l'interprétation est compromise

### Le VIF (Variance Inflation Factor)

Le **VIF** mesure à quel point la variance d'un coefficient de régression est **gonflée** à cause de la multicolinéarité.

**Formule** : VIF_j = 1 / (1 - R²_j)

Où R²_j est le R² obtenu en régressant la variable X_j sur toutes les autres variables explicatives.

**Interprétation** :

| Valeur VIF | Interprétation | Action |
|------------|----------------|--------|
| **VIF = 1** | Aucune corrélation avec les autres X | Parfait ✓ |
| **VIF < 5** | Multicolinéarité faible à modérée | Acceptable ✓ |
| **5 ≤ VIF < 10** | Multicolinéarité modérée à forte | Attention ⚠️ |
| **VIF ≥ 10** | Multicolinéarité sévère | Problème ✗ |

**Exemple d'interprétation** :
- VIF(température) = 2.5 → La variance du coefficient de température est **2.5 fois plus élevée** qu'elle ne le serait si température était totalement indépendante des autres variables

### Que faire si VIF élevé ?

Si VIF ≥ 10 (multicolinéarité sévère) :

1. **Retirer une variable** : Supprimer l'une des variables corrélées
   ```r
   # Si température et oxygène sont trop corrélés,
   # garder celle qui a le plus fort lien avec Y
   ```

2. **Combiner les variables** : Créer un indice composite
   ```r
   # Créer un score combiné (ex: analyse en composantes principales)
   ```

3. **Augmenter la taille de l'échantillon** : Plus de données peuvent réduire l'instabilité

4. **Régression ridge** : Méthode de régression pénalisée robuste à la multicolinéarité
   ```r
   library(glmnet)
   # Utiliser ridge regression
   ```

### Exemple concret en aquaculture

**Cas 1 : Pas de multicolinéarité**
```r
vif(modele2)
# temperature    oxygene
#      1.2          1.2
```
→ Les deux variables sont presque indépendantes → Coefficients fiables ✓

**Cas 2 : Multicolinéarité sévère**
```r
vif(modele2)
# temperature    oxygene
#      12.5        12.5
```
→ Température et oxygène sont très corrélés (eau chaude = moins d'O₂)
→ Impossible de séparer leurs effets
→ **Solution** : Retirer l'une des deux ou créer un indice "qualité de l'eau"

### Dans notre cas

La température et l'oxygène dissous sont **négativement corrélés** en aquaculture (l'eau chaude contient moins d'oxygène dissous), mais cette corrélation est généralement **modérée** (r ≈ -0.4 à -0.6), ce qui donne des VIF < 5 → **pas de problème**.

---

**Résultat** :

### 3.4 Comparaison de modèles

**Question 3.10** : Comparez formellement les deux modèles avec un test ANOVA.

```r
# Votre code ici
anova(modele1, modele2)
```

**Hypothèses du test** :
- H₀ : Les deux modèles sont équivalents (modèle simple suffit)
- H₁ : Le modèle multiple est significativement meilleur

**Conclusion** (p < 0.05 = modèle multiple meilleur) :

**Question 3.11** : En conclusion, quel modèle retenez-vous pour prédire le taux de croissance de la daurade ? Justifiez en utilisant au moins trois critères.

**Réponse :**

---

## PARTIE 4 : Introduction aux GLM - Modèles Linéaires Généralisés

### 4.1 Principe des GLM

Les **Modèles Linéaires Généralisés** (GLM) sont une extension des modèles linéaires classiques qui permettent de modéliser des données qui ne suivent pas une distribution normale.

**Limites de la régression linéaire classique** :
- Suppose que Y suit une loi normale
- Suppose que la variance est constante
- Ne convient pas pour :
  - Données de **comptage** (0, 1, 2, 3, ...) → distribution de Poisson
  - Données **binaires** (0/1, succès/échec) → distribution binomiale
  - Données de **proportions** (0-1) → distribution binomiale

**Composantes d'un GLM** :
1. **Composante aléatoire** : distribution de Y (normale, Poisson, binomiale, ...)
2. **Composante systématique** : prédicteur linéaire (β₀ + β₁X₁ + ...)
3. **Fonction de lien** : relie la moyenne de Y au prédicteur linéaire

**GLM courants en biologie** :

| Type de données | Distribution | Fonction de lien | Exemple |
|-----------------|--------------|------------------|---------|
| Continue, normale | Normale (Gaussian) | Identité | Taille, poids |
| Comptage | Poisson | Log | Nombre de poissons, œufs |
| Binaire | Binomiale | Logit | Survie/mort, présence/absence |
| Proportion | Binomiale | Logit | Taux de survie |

### 4.2 Exemple : Régression de Poisson pour données de comptage

Nous allons analyser les données de **plancton** où la variable **abondance** est un comptage (nombre d'organismes).

**Question 4.1** : Affichez les premières lignes du jeu de données `plancton`.

```r
# Votre code ici
head(plancton)
str(plancton)
```

**Variables** :
- `temperature` : température de l'eau (°C)
- `chlorophylle` : concentration en chlorophylle-a (µg/L), indicateur de production primaire
- `abondance` : nombre d'organismes planctoniques comptés (variable de comptage)

**Question 4.2** : Créez un histogramme de la variable `abondance`.

```r
# Votre code ici
```

**Question 4.3** : La distribution de l'abondance ressemble-t-elle à une loi normale ? Pourquoi ?

**Réponse :**

**Question 4.4** : Essayez d'abord une régression linéaire classique (inappropriée, mais à titre de comparaison).

```r
# Modèle linéaire classique (INCORRECT pour des comptages)
modele_lm <- lm(abondance ~ chlorophylle, data = plancton)
summary(modele_lm)

# Graphique de diagnostic
par(mfrow = c(2, 2))
plot(modele_lm)
par(mfrow = c(1, 1))
```

**Problèmes observés** (à compléter après avoir vu les graphiques) :

**Question 4.5** : Construisez maintenant un GLM avec distribution de Poisson (modèle approprié).

```r
# Votre code ici
# GLM Poisson
modele_glm <- glm(abondance ~ chlorophylle,
                  data = plancton,
                  family = poisson(link = "log"))

summary(modele_glm)
```

**Interprétation des résultats** :

Le modèle est :
```
log(abondance) = β₀ + β₁ × chlorophylle
```

Ou de manière équivalente :
```
abondance = exp(β₀ + β₁ × chlorophylle)
```

**Question 4.6** : Complétez le tableau :

| Paramètre | Estimation | Erreur standard | z-value | p-value | Significatif ? |
|-----------|------------|-----------------|---------|---------|----------------|
| β₀ (Intercept) |        |                 |         |         |                |
| β₁ (chlorophylle) |        |                 |         |         |                |

**Question 4.7** : Interprétez le coefficient de la chlorophylle.

**Rappel** : Dans un GLM Poisson avec lien log, l'effet d'une augmentation de 1 unité de X est multiplicatif :
- Si β₁ = 0.05, alors exp(0.05) = 1.051, soit une augmentation de 5.1% de l'abondance pour chaque µg/L de chlorophylle en plus.

**Réponse :**

**Question 4.8** : Évaluez la qualité du modèle GLM.

```r
# 1. Déviance résiduelle vs degrés de liberté
summary(modele_glm)$deviance / summary(modele_glm)$df.residual

# 2. Test du rapport de vraisemblance (comparaison avec modèle nul)
modele_nul <- glm(abondance ~ 1, data = plancton, family = poisson)
anova(modele_nul, modele_glm, test = "Chisq")

# 3. Pseudo-R² (R² de McFadden)
1 - (summary(modele_glm)$deviance / summary(modele_glm)$null.deviance)
```

**Interprétation** :
- Ratio déviance/df proche de 1 = bon ajustement
- Test du rapport de vraisemblance : p < 0.05 = modèle significativement meilleur que le modèle nul

**Résultats** :

**Question 4.9** : Visualisez le modèle GLM.

```r
# Créer des prédictions
nouveaux_chloro <- data.frame(chlorophylle = seq(min(plancton$chlorophylle),
                                                   max(plancton$chlorophylle),
                                                   length.out = 100))

predictions_glm <- predict(modele_glm, newdata = nouveaux_chloro,
                           type = "response", se.fit = TRUE)

nouveaux_chloro$fit <- predictions_glm$fit
nouveaux_chloro$lwr <- predictions_glm$fit - 1.96 * predictions_glm$se.fit
nouveaux_chloro$upr <- predictions_glm$fit + 1.96 * predictions_glm$se.fit

# Graphique
ggplot() +
  geom_point(data = plancton, aes(x = chlorophylle, y = abondance),
             color = "seagreen", size = 3, alpha = 0.6) +
  geom_line(data = nouveaux_chloro, aes(x = chlorophylle, y = fit),
            color = "darkgreen", size = 1.2) +
  geom_ribbon(data = nouveaux_chloro,
              aes(x = chlorophylle, ymin = lwr, ymax = upr),
              alpha = 0.2, fill = "darkgreen") +
  labs(title = "Modèle GLM Poisson : Abondance vs Chlorophylle",
       x = "Concentration en chlorophylle (µg/L)",
       y = "Abondance planctonique (nombre)") +
  theme_minimal()
```

**Question 4.10** : Comparez visuellement la régression linéaire classique et le GLM Poisson.

```r
# Prédictions du modèle linéaire
predictions_lm <- predict(modele_lm, newdata = nouveaux_chloro, interval = "confidence")
nouveaux_chloro$fit_lm <- predictions_lm[, "fit"]

# Graphique comparatif
ggplot() +
  geom_point(data = plancton, aes(x = chlorophylle, y = abondance),
             color = "black", size = 3, alpha = 0.5) +
  geom_line(data = nouveaux_chloro, aes(x = chlorophylle, y = fit, color = "GLM Poisson"),
            size = 1.2) +
  geom_line(data = nouveaux_chloro, aes(x = chlorophylle, y = fit_lm, color = "Régression linéaire"),
            size = 1.2, linetype = "dashed") +
  scale_color_manual(values = c("GLM Poisson" = "darkgreen",
                                 "Régression linéaire" = "red")) +
  labs(title = "Comparaison : Régression linéaire vs GLM Poisson",
       x = "Concentration en chlorophylle (µg/L)",
       y = "Abondance planctonique (nombre)",
       color = "Modèle") +
  theme_minimal() +
  theme(legend.position = "bottom")
```

**Question 4.11** : Quelles différences observez-vous entre les deux modèles ? Lequel est le plus approprié et pourquoi ?

**Réponse :**

### 4.3 Test de surdispersion

Un problème fréquent avec les GLM Poisson est la **surdispersion** : la variance observée est supérieure à la variance théorique de la loi de Poisson (où variance = moyenne).

**Question 4.12** : Testez la surdispersion.

```r
# Test de surdispersion
# Si ratio >> 1, il y a surdispersion
dispersion <- sum(residuals(modele_glm, type = "pearson")^2) / modele_glm$df.residual
dispersion
```

**Interprétation** :
- Ratio ≈ 1 : pas de surdispersion (hypothèse de Poisson respectée)
- Ratio > 1.5 : surdispersion → utiliser GLM quasi-Poisson ou binomiale négative

**Résultat** :

**Question 4.13** : Si surdispersion détectée, ajustez un modèle quasi-Poisson.

```r
# Votre code ici
modele_qp <- glm(abondance ~ chlorophylle,
                 data = plancton,
                 family = quasipoisson(link = "log"))

summary(modele_qp)
```

**Comparaison** : Les estimations des coefficients sont identiques, mais les erreurs standard et p-values sont corrigées.

---

## PARTIE 5 : GLM Binomial - Régression logistique

Cette partie est optionnelle mais utile pour comprendre un autre type de GLM très utilisé en biologie.

### 5.1 Contexte

Imaginons qu'on ait mesuré la **survie** (0 = mort, 1 = vivant) de daurades en fonction de la température de l'eau. Ce type de données binaires nécessite une **régression logistique** (GLM avec distribution binomiale).

**Question 5.1** : Générez des données de survie simulées.

```r
# Génération de données de survie
set.seed(456)
temperature_survie <- seq(10, 35, length.out = 100)

# Probabilité de survie diminue avec la température extrême
# Optimum autour de 20°C
prob_survie <- exp(-((temperature_survie - 20)^2) / 50)

# Simulation de survie (0/1)
survie <- rbinom(100, size = 1, prob = prob_survie)

# Créer le dataframe
donnees_survie <- data.frame(temperature = temperature_survie, survie = survie)

# Visualiser
table(donnees_survie$survie)
```

**Question 5.2** : Créez un graphique montrant la proportion de survie par classe de température.

```r
# Votre code ici
ggplot(donnees_survie, aes(x = temperature, y = survie)) +
  geom_point(alpha = 0.5, size = 2) +
  geom_smooth(method = "glm", method.args = list(family = "binomial"),
              se = TRUE, color = "red") +
  labs(title = "Survie de la daurade en fonction de la température",
       x = "Température (°C)",
       y = "Survie (0 = mort, 1 = vivant)") +
  theme_minimal()
```

**Question 5.3** : Ajustez un modèle de régression logistique.

```r
# Votre code ici
modele_logit <- glm(survie ~ temperature,
                    data = donnees_survie,
                    family = binomial(link = "logit"))

summary(modele_logit)
```

**Modèle** :
```
logit(P(survie = 1)) = log(P / (1-P)) = β₀ + β₁ × température
```

**Question 5.4** : Calculez les odds ratios.

```r
# Votre code ici
# Odds ratio pour une augmentation de 1°C
exp(coef(modele_logit))

# Intervalle de confiance 95%
exp(confint(modele_logit))
```

**Interprétation** : Un odds ratio < 1 indique que la probabilité de survie diminue quand la température augmente.

---

## SYNTHÈSE GÉNÉRALE

**Question finale** : Rédigez une synthèse (400 mots maximum) présentant :

1. **Corrélation de Pearson** :
   - Quelles relations avez-vous identifiées ?
   - Quelle est la différence entre corrélation et causalité ?

2. **Régression linéaire** :
   - Quelle est la différence entre corrélation et régression ?
   - Quel modèle (simple ou multiple) est le meilleur pour prédire la croissance de la daurade ? Pourquoi ?

3. **GLM** :
   - Pourquoi utilise-t-on un GLM Poisson plutôt qu'une régression linéaire pour des données de comptage ?
   - Dans quelles situations biologiques utiliseriez-vous un GLM ?

4. **Recommandations aquacoles** :
   - Quels sont les facteurs clés influençant la croissance de la daurade ?
   - Quelles conditions optimales recommandez-vous pour maximiser la production ?

**Votre synthèse :**

---

## Pour aller plus loin (Optionnel)

### 1. Régression polynomiale
Si la relation n'est pas linéaire, essayez une régression polynomiale :

```r
modele_poly <- lm(taux_croissance ~ poly(temperature, 2), data = daurade)
```

### 2. GLM binomiale négative
Pour des données de comptage avec forte surdispersion :

```r
library(MASS)
modele_nb <- glm.nb(abondance ~ chlorophylle, data = plancton)
```

### 3. Sélection de modèles avec AIC
Comparer plusieurs modèles avec le critère d'Akaike :

```r
AIC(modele1, modele2)
```

### 4. Régression robuste
Si présence de valeurs aberrantes :

```r
library(MASS)
modele_robust <- rlm(taux_croissance ~ temperature, data = daurade)
```

### 5. Modèles additifs généralisés (GAM)
Pour des relations non-linéaires complexes :

```r
library(mgcv)
modele_gam <- gam(taux_croissance ~ s(temperature) + s(oxygene), data = daurade)
```

---

## Ressources complémentaires

**Documentation R** :
- `?cor` et `?cor.test`
- `?lm`
- `?glm`
- `?predict`

**Packages** :
- `ggplot2` : visualisations avancées
- `corrplot` : matrices de corrélation
- `car` : diagnostics de régression
- `MASS` : GLM binomiale négative
- `mgcv` : modèles additifs généralisés

**Références** :
- Zuur, A.F., Ieno, E.N., & Elphick, C.S. (2010). A protocol for data exploration to avoid common statistical problems. *Methods in Ecology and Evolution*, 1(1), 3-14.
- Crawley, M.J. (2013). *The R Book*. 2nd edition. Wiley.
- Faraway, J.J. (2016). *Extending the Linear Model with R*. 2nd edition. Chapman & Hall/CRC.

---
