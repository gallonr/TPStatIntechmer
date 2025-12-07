# TP : Analyse de variance (ANOVA) en aquaculture
## Étude de la croissance de la truite arc-en-ciel (*Oncorhynchus mykiss*)

---

## Contexte biologique

Une ferme aquacole souhaite optimiser la production de truites arc-en-ciel. Deux expériences ont été menées :

### Expérience 1 : Effet du régime alimentaire
Tester l'effet de **3 régimes alimentaires** différents sur la croissance :
- **Régime A** : Granulés commerciaux standard (témoin)
- **Régime B** : Granulés enrichis en protéines
- **Régime C** : Granulés enrichis en lipides

### Expérience 2 : Effet du régime et de la densité
Tester l'effet combiné du **régime alimentaire** (A, B, C) et de la **densité d'élevage** (Faible, Élevée) sur la croissance.

**Variable mesurée** : Gain de poids (en grammes) après 12 semaines d'élevage

---

## Préparation de l'environnement de travail

### Installation et chargement des packages

```r
# Installation des packages (si nécessaire)
install.packages(c("ggplot2", "dplyr", "FSA", "DescTools", "dunn.test", "multcomp"))

# Chargement des packages
library(ggplot2)    # Pour les graphiques
library(dplyr)      # Pour la manipulation de données
library(FSA)        # Pour le test de Dunn
library(DescTools)  # Pour les tests post-hoc
library(dunn.test)  # Pour le test de Dunn alternatif
library(multcomp)   # Pour les lettres de groupes (optionnel, Question 4.16)
```

### Génération et chargement des données

```r
# Définir le répertoire de travail
setwd("chemin/vers/votre/dossier")

# exp1 objet contenant "experience1_regime.csv"
# exp2 objet contenant "experience2_regime_densite.csv"

# Convertir les variables catégorielles en facteurs
exp1$regime <- factor(exp1$regime, levels = c("A", "B", "C"))
exp2$regime <- factor(exp2$regime, levels = c("A", "B", "C"))
exp2$densite <- factor(exp2$densite, levels = c("Faible", "Elevee"))

# Visualiser la structure des données
str(exp1)
head(exp1)

str(exp2)
head(exp2)
```

---

## PARTIE 1 : Statistiques univariées (30 min)

### 1.1 Exploration des données de l'expérience 1

**Question 1.1** : Affichez les dimensions du jeu de données `exp1` et vérifiez qu'il contient bien 2 colonnes : `regime` et `gain_poids`.

**Question 1.2** : Combien d'observations (truites) avez-vous pour chaque régime ?

```r
# Indice : utilisez la fonction count()
```

**Question 1.3** : Calculez les statistiques descriptives suivantes **pour chaque régime** :
- Moyenne
- Médiane
- écart-type
- Minimum et maximum
- Premier et troisième quartile (Q1 et Q3)

```r
# Indice : utilisez la fonction aggregate() ou tapply()
```

**Question 1.4** : Créez un tableau résumé avec la moyenne, l'écart-type et le nombre d'observations par régime en utilisant `dplyr`.

```r
# Indice : group_by() et summarise()
```

### 1.2 Visualisation des données

**Question 1.5** : Créez un histogramme du gain de poids pour l'ensemble des données (tous régimes confondus).

```r
# Votre code ici avec ggplot2
```

**Question 1.6** : Créez des boîtes à moustaches (*boxplots*) du gain de poids pour chaque régime.

```r
# Utilisez ggplot2 avec geom_boxplot()
```

**Question 1.7** : Créez un graphique en barres montrant les moyennes avec les barres d'erreur (± écart-type) pour chaque régime.

```r
# Indice : créez d'abord un dataframe résumé, puis utilisez geom_bar() et geom_errorbar()
```

**Question 1.8** : À partir des graphiques et statistiques descriptives, formulez une première hypothèse : y a-t-il une différence apparente entre les régimes ? Lequel semble donner les meilleurs résultats ?

**Réponse :**

---

## PARTIE 2 : ANOVA à un facteur (45 min)

### 2.1 Rappel théorique

L'ANOVA (Analysis of Variance) permet de tester l'égalité des moyennes de plusieurs groupes.

**Hypothèses statistiques :**
- H₀ : μ_A = μ_B = μ_C (les moyennes sont égales)
- H₁ : Au moins une moyenne diffère

**Conditions d'application :**
1. **Normalité** des résidus
2. **Homogénéité des variances** (homoscédasticité)
3. **Indépendance** des observations (assuré par le plan expérimental)

### 2.2 Vérification des conditions d'application

Avant de réaliser une ANOVA, il est **essentiel** de vérifier que les données respectent les hypothèses du test :
1. **Normalité des résidus** : les résidus doivent suivre une loi normale
2. **Homogénéité des variances** : les variances doivent être égales entre les groupes
3. **Indépendance des observations** : déjà assurée par le plan expérimental

**Question 2.1** : Créez le modèle linéaire et testez la normalité des résidus avec un test de Shapiro-Wilk.

**Rappel** : Les résidus sont les écarts entre les valeurs observées et les valeurs prédites par le modèle.
- Résidu = Valeur observée - Valeur prédite (moyenne du groupe)

```r
# Créer le modèle ANOVA
modele1 <- lm(gain_poids ~ regime, data = exp1)

# Extraire les résidus
residus <- residuals(modele1)
# OU : residus <- resid(modele1)

# Test de normalité sur les résidus
# Votre code ici : shapiro.test(...)
```

**Interprétation** :
- Si p > 0.05 : les résidus suivent une loi normale (hypothèse respectée ✓)
- Si p < 0.05 : les résidus ne suivent pas une loi normale (→ ANOVA robuste ou Kruskal-Wallis)

**Question 2.2** : Testez l'homogénéité des variances (homoscédasticité) avec le test de Bartlett.

Le test de Bartlett teste l'hypothèse H₀ : σ²_A = σ²_B = σ²_C (variances égales).

```r
# Votre code ici
# Utilisez bartlett.test()
```

**Interprétation** :
- Si p > 0.05 : les variances sont homogènes (hypothèse respectée ✓)
- Si p < 0.05 : les variances sont hétérogènes (utiliser Welch ANOVA ou transformation)

**Note** : Le test de Bartlett est sensible à la non-normalité. Si les données ne sont pas normales, préférer le test de Fligner-Killeen.

**Question 2.3** : Créez des graphiques de diagnostic des résidus.

**Méthode 1 : Graphiques automatiques avec plot()**

```r
# Afficher les 4 graphiques de diagnostic
par(mfrow = c(2, 2))  # 2 lignes, 2 colonnes
plot(modele1)
par(mfrow = c(1, 1))  # Revenir à 1 graphique
```

**Interprétation des 4 graphiques :**

1. **Residuals vs Fitted** (Résidus vs Valeurs ajustées)
   - **But** : Vérifier l'homoscédasticité (variance constante)
   - **Attendu** : Points dispersés aléatoirement autour de 0, sans motif
   - **⚠️ Problème si** : Forme en entonnoir, courbe, ou tendance

2. **Normal Q-Q** (Quantile-Quantile)
   - **But** : Vérifier la normalité des résidus
   - **Attendu** : Points alignés sur la diagonale
   - **⚠️ Problème si** : Points s'écartent fortement de la droite

3. **Scale-Location** (√Résidus standardisés vs Valeurs ajustées)
   - **But** : Vérifier l'homoscédasticité (autre visualisation)
   - **Attendu** : Ligne horizontale, points dispersés uniformément
   - **⚠️ Problème si** : Tendance croissante ou décroissante

4. **Residuals vs Leverage** (Résidus vs Effet de levier)
   - **But** : Identifier les valeurs influentes (outliers)
   - **Attendu** : Tous les points dans la zone (distance de Cook < 0.5)
   - **⚠️ Problème si** : Points au-delà des lignes de Cook (pointillés)

**Méthode 2 : Graphiques personnalisés avec ggplot2**

```r
# Créer un dataframe avec les résidus et valeurs ajustées
diag_data <- data.frame(
  fitted = fitted(modele1),
  residuals = residuals(modele1),
  std_residuals = rstandard(modele1),
  regime = exp1$regime
)

# 1. Résidus vs Valeurs ajustées
library(ggplot2)
ggplot(diag_data, aes(x = fitted, y = residuals)) +
  geom_point(aes(color = regime), size = 2) +
  geom_hline(yintercept = 0, linetype = "dashed", color = "red") +
  geom_smooth(se = FALSE, color = "blue") +
  labs(title = "Résidus vs Valeurs ajustées",
       x = "Valeurs ajustées (moyennes de groupe)",
       y = "Résidus") +
  theme_minimal()

# 2. QQ-plot (normalité)
ggplot(diag_data, aes(sample = residuals)) +
  stat_qq() +
  stat_qq_line(color = "red") +
  labs(title = "QQ-plot : Normalité des résidus",
       x = "Quantiles théoriques",
       y = "Quantiles observés") +
  theme_minimal()

# 3. Histogramme des résidus
ggplot(diag_data, aes(x = residuals)) +
  geom_histogram(bins = 15, fill = "steelblue", color = "black", alpha = 0.7) +
  geom_vline(xintercept = 0, linetype = "dashed", color = "red") +
  labs(title = "Distribution des résidus",
       x = "Résidus",
       y = "Fréquence") +
  theme_minimal()

# 4. Boxplot des résidus par groupe
ggplot(diag_data, aes(x = regime, y = residuals, fill = regime)) +
  geom_boxplot(alpha = 0.7) +
  geom_hline(yintercept = 0, linetype = "dashed", color = "red") +
  labs(title = "Résidus par régime (vérification homoscédasticité)",
       x = "Régime",
       y = "Résidus") +
  theme_minimal() +
  theme(legend.position = "none")
```

**À retenir** :
- Si tous les graphiques sont satisfaisants → Les hypothèses sont respectées ✓
- Si normalité non respectée → Envisager Kruskal-Wallis
- Si homoscédasticité non respectée → Envisager Welch ANOVA ou transformation

### 2.3 Réalisation de l'ANOVA

**Question 2.4** : Réalisez l'ANOVA à un facteur et affichez le tableau d'analyse de variance.

```r
# Votre code ici
# Utilisez anova() ou summary(aov(...))
```

**Question 2.5** : Interprétez les résultats en complétant le tableau suivant :

| Source de variation | Degrés de liberté | Somme des carrés | Carré moyen | F | p-value |
|---------------------|-------------------|------------------|-------------|---|---------|
| Régime              |                   |                  |             |   |         |
| Résidus             |                   |                  |             |   |         |

**Conclusion statistique** (au seuil α = 0.05) :

**Question 2.6** : Calculez la part de variance expliquée par le régime (Ré).

```r
# Votre code ici
# R² = SS_régime / SS_total
```

Interprétation : Le régime alimentaire explique ___% de la variabilité du gain de poids.

### 2.4 Tests post-hoc

Si l'ANOVA est significative (p < 0.05), il faut identifier **quels groupes** diffèrent entre eux.

**Question 2.7** : Réalisez un test post-hoc de Tukey HSD (Honestly Significant Difference).

```r
# Votre code ici
# Utilisez TukeyHSD()
```

**Question 2.8** : Complétez le tableau des comparaisons deux à deux :

| Comparaison | Différence de moyennes | p-value ajustée | Significatif ? |
|-------------|------------------------|-----------------|----------------|
| B - A       |                        |                 |                |
| C - A       |                        |                 |                |
| C - B       |                        |                 |                |

**Question 2.9** : Créez une visualisation graphique des comparaisons multiples.

```r
# Votre code ici
# Utilisez plot(TukeyHSD(...)) ou un graphique ggplot2 personnalisé
```

**Question 2.10** : Synthèse - Quels régimes diffèrent significativement ? Quel régime recommanderiez-vous à l'aquaculteur ?

**Réponse :**

---

## PARTIE 3 : Test de Kruskal-Wallis et post-hoc de Dunn

### 3.1 Principe du test

Le test de **Kruskal-Wallis** est l'alternative non-paramétrique de l'ANOVA à un facteur. Il compare les **médianes** de plusieurs groupes en se basant sur les **rangs** des observations.

**Quand l'utiliser ?**
- Lorsque la normalité des résidus n'est pas respectée
- Lorsque l'homogénéité des variances n'est pas respectée
- Pour des données ordinales

**Question 3.1** : Expliquez brièvement la différence entre l'ANOVA et le test de Kruskal-Wallis.

**Réponse :**

### 3.2 Réalisation du test de Kruskal-Wallis

**Question 3.2** : Réalisez le test de Kruskal-Wallis sur les données de l'expérience 1.

```r
# Votre code ici
# Utilisez kruskal.test()
```

**Question 3.3** : Interprétez les résultats :

- Statistique du test (H ou chi²) :
- Degrés de liberté :
- p-value :
- Conclusion (au seuil α = 0.05) :

**Question 3.4** : Comparez la conclusion du test de Kruskal-Wallis avec celle de l'ANOVA. Obtenez-vous la même conclusion ?

**Réponse :**

### 3.3 Test post-hoc de Dunn

Si le test de Kruskal-Wallis est significatif, on utilise le **test de Dunn** pour les comparaisons multiples.

**Question 3.5** : Réalisez un test post-hoc de Dunn avec correction de Bonferroni.

```r
# Votre code ici
# Méthode 1 : package dunn.test
library(dunn.test)
dunn.test(exp1$gain_poids, exp1$regime, method = "bonferroni")

# OU Méthode 2 : package FSA
library(FSA)
dunnTest(gain_poids ~ regime, data = exp1, method = "bonferroni")
```

**Question 3.6** : Complétez le tableau des comparaisons :

| Comparaison | Statistique Z | p-value ajustée | Significatif ? |
|-------------|---------------|-----------------|----------------|
| B - A       |               |                 |                |
| C - A       |               |                 |                |
| C - B       |               |                 |                |

**Question 3.7** : Comparez les résultats du test de Dunn avec ceux du test de Tukey. Y a-t-il des différences ? Si oui, pourquoi ?

**Réponse :**

---

## PARTIE 4 : ANOVA à deux facteurs 

Nous allons maintenant analyser l'**expérience 2** qui étudie l'effet combiné du régime alimentaire et de la densité d'élevage.

### 4.1 Exploration et visualisation des données

**Question 4.1** : Affichez la structure des données `exp2` et vérifiez qu'il y a bien 3 colonnes.

```r
# Votre code ici
str(exp2)
head(exp2)
```

**Question 4.2** : Calculez le nombre d'observations pour chaque combinaison régime × densité.

```r
# Votre code ici
count(exp2$regime, exp2$densite)
```

Le plan expérimental est-il équilibré ?

**Question 4.3** : Calculez les statistiques descriptives (moyenne et écart-type) pour chaque combinaison de régime et de densité.

```r
# Votre code ici
# Utilisez aggregate() ou group_by() + summarise()
```

**Question 4.4** : Créez un graphique d'interaction montrant les moyennes pour chaque combinaison.

**Méthode 1 : Fonction de base R**

```r
# Graphique d'interaction simple avec interaction.plot()
interaction.plot(
  x.factor = exp2$densite,           # Axe X
  trace.factor = exp2$regime,        # Lignes (une par régime)
  response = exp2$gain_poids,        # Variable Y
  fun = mean,                        # Fonction à appliquer (moyenne)
  type = "b",                        # Type : "b" = points + lignes
  legend = TRUE,
  xlab = "Densité d'élevage",
  ylab = "Gain de poids moyen (g)",
  col = c("orange", "steelblue", "green"),
  lwd = 2,                          # Épaisseur des lignes
  pch = c(15, 16, 17),              # Formes des points
  main = "Graphique d'interaction : Régime × Densité"
)
```

**Méthode 2 : ggplot2 (recommandé - plus esthétique)**

```r
# 1. Créer un dataframe résumé avec les moyennes
library(dplyr)
resume_exp2 <- exp2 %>%
  group_by(regime, densite) %>%
  summarise(
    moyenne = mean(gain_poids),
    ecart_type = sd(gain_poids),
    .groups = 'drop'
  )

# 2. Créer le graphique d'interaction
library(ggplot2)
ggplot(resume_exp2, aes(x = densite, y = moyenne,
                        color = regime, group = regime)) +
  geom_line(size = 1.2) +                    # Lignes
  geom_point(size = 4) +                     # Points
  geom_errorbar(aes(ymin = moyenne - ecart_type,
                    ymax = moyenne + ecart_type),
                width = 0.1) +               # Barres d'erreur
  scale_color_manual(values = c("A" = "#E69F00",
                                 "B" = "#56B4E9",
                                 "C" = "#009E73")) +
  labs(title = "Graphique d'interaction : Régime × Densité",
       subtitle = "Gain de poids de la truite arc-en-ciel",
       x = "Densité d'élevage",
       y = "Gain de poids moyen (g)",
       color = "Régime") +
  theme_minimal() +
  theme(plot.title = element_text(face = "bold", size = 14),
        legend.position = "right")
```

**À observer sur votre graphique** :
- Les lignes sont-elles parallèles ou se croisent-elles ?
- Quelle ligne est la plus haute ? (meilleur régime)
- Les lignes descendent-elles de Faible à Élevée ? (effet densité)
- Les pentes sont-elles différentes entre les régimes ? (interaction)

**Question 4.5** : À partir du graphique d'interaction, que pouvez-vous dire sur :
- L'effet du régime ?
- L'effet de la densité ?
- La présence d'une interaction régime × densité ?

---

**💡 AIDE À L'INTERPRÉTATION DU GRAPHIQUE D'INTERACTION**

Un **graphique d'interaction** représente la moyenne de la variable dépendante (gain de poids) pour chaque combinaison des deux facteurs. On trace généralement :
- Un facteur sur l'axe X (ex: densité)
- Une ligne pour chaque niveau de l'autre facteur (ex: une ligne par régime)

### 📊 Comment interpréter le graphique ?

#### 1️⃣ **Effet principal du RÉGIME**
Comparez les lignes entre elles (verticalement) :
- Si les lignes sont **à des hauteurs différentes** → Il y a un effet du régime
- Si les lignes sont **à la même hauteur** → Pas d'effet du régime

**Question** : Quel régime donne les meilleurs résultats en moyenne ?

#### 2️⃣ **Effet principal de la DENSITÉ**
Regardez la pente générale des lignes :
- Si les lignes ont une **tendance à monter ou descendre** → Il y a un effet de la densité
- Si les lignes sont **horizontales** → Pas d'effet de la densité

**Question** : Le gain de poids augmente-t-il ou diminue-t-il avec la densité élevée ?

#### 3️⃣ **INTERACTION régime × densité**
Examinez le parallélisme des lignes :

**❌ PAS D'INTERACTION** (lignes parallèles)
```
    B ————————————————
    C ————————————————
    A ————————————————
  Faible         Élevée
```
→ L'effet de la densité est **le même** pour tous les régimes

**✅ INTERACTION PRÉSENTE** (lignes non parallèles)
```
    B ╲
    C  ╲___
    A   ╲____
  Faible    Élevée
```
→ L'effet de la densité **dépend** du régime

**Signes d'interaction** :
- Lignes qui **se croisent** → Interaction forte
- Lignes avec des **pentes différentes** → Interaction
- Une ligne monte, une autre descend → Interaction forte

**Interprétation biologique** :
Si interaction → Certains régimes sont plus sensibles que d'autres à la densité d'élevage

---

**Réponse :** (à compléter après avoir observé votre graphique)

### 4.2 Vérification des conditions d'application

**Question 4.6** : Créez le modèle ANOVA à 2 facteurs avec interaction et testez la normalité des résidus.

```r
# Créer le modèle ANOVA à 2 facteurs
modele2 <- lm(gain_poids ~ regime * densite, data = exp2)
# OU équivalent : lm(gain_poids ~ regime + densite + regime:densite, data = exp2)

# Test de normalité
# Votre code ici
```

**Question 4.7** : Testez l'homogénéité des variances avec le test de Bartlett.

```r
# Votre code ici
# Utilisez bartlett.test()
# Indice : créez une variable combinée ou utilisez la formule
```

Les conditions d'application sont-elles respectées ?

### 4.3 Réalisation de l'ANOVA à deux facteurs

**Question 4.8** : Réalisez l'ANOVA à deux facteurs avec interaction.

```r
# Votre code ici
# Utilisez anova(modele2) ou Anova(modele2, type = "III")
```

**Question 4.9** : Complétez le tableau d'ANOVA :

| Source de variation | Degrés de liberté | Somme des carrés | Carré moyen | F | p-value |
|---------------------|-------------------|------------------|-------------|---|---------|
| Régime              |                   |                  |             |   |         |
| Densité             |                   |                  |             |   |         |
| Régime × Densité    |                   |                  |             |   |         |
| Résidus             |                   |                  |             |   |         |

**Question 4.10** : Interprétez les résultats en répondant aux questions suivantes :

a) L'effet principal du **régime** est-il significatif ? (α = 0.05)

b) L'effet principal de la **densité** est-il significatif ? (α = 0.05)

c) L'**interaction** régime × densité est-elle significative ? (α = 0.05)

d) Si l'interaction est significative, que signifie-t-elle biologiquement ?

**Réponses :**

### 4.4 Interprétation de l'interaction

**Question 4.11** : Revenez au graphique d'interaction (Question 4.4). Comment les lignes se comportent-elles ?
- Des lignes **parallèles** indiquent une absence d'interaction
- Des lignes **non parallèles** indiquent une interaction

Décrivez l'interaction observée en termes biologiques.

**Réponse :**

### 4.5 Tests post-hoc pour ANOVA à deux facteurs

Lorsqu'il y a une interaction significative, il faut analyser les **effets simples** : l'effet d'un facteur à chaque niveau de l'autre facteur.

**Question 4.12** : Réalisez des tests post-hoc de Tukey pour toutes les combinaisons possibles.

```r
# Votre code ici
# Méthode 1 : Créer une variable combinée
exp2$combinaison <- interaction(exp2$regime, exp2$densite)
modele_combine <- aov(gain_poids ~ combinaison, data = exp2)
TukeyHSD(modele_combine)

# Méthode 2 : Utiliser PostHocTest du package DescTools
```

**Question 4.13** : Identifiez les groupes qui diffèrent significativement. Complétez le tableau suivant avec les comparaisons les plus importantes :

| Comparaison | Différence | p-value | Significatif ? |
|-------------|------------|---------|----------------|
| B_Faible - A_Faible |     |         |                |
| B_Faible - B_Elevee |     |         |                |
| B_Faible - C_Faible |     |         |                |

**Question 4.14** : Pour chaque régime séparément, testez l'effet de la densité.

```r
# Votre code ici
# Filtrer les données par régime et comparer Faible vs Élevée
```

La densité a-t-elle le même effet pour tous les régimes ?

### 4.6 Visualisation finale

**Question 4.15** : Créez un graphique final avec des barres d'erreur montrant toutes les combinaisons régime × densité.

```r
# Votre code ici
# Utilisez ggplot2 avec geom_bar(), geom_errorbar() et facet_wrap() ou dodge
```

**Question 4.16** : Ajoutez les lettres de groupes issus des tests post-hoc sur le graphique (groupes homogènes).

**Principe** : Les lettres identiques indiquent des groupes statistiquement non différents. Les groupes avec des lettres différentes sont significativement différents.

**Méthode 1 : Avec le package multcomp (automatique)**

```r
# Installation si nécessaire
# install.packages("multcomp")

library(multcomp)

# Créer une variable combinée pour les tests post-hoc
exp2$combinaison <- interaction(exp2$regime, exp2$densite, sep = "_")
modele_combine <- aov(gain_poids ~ combinaison, data = exp2)

# Générer les lettres de groupes automatiquement
cld_result <- cld(glht(modele_combine, linfct = mcp(combinaison = "Tukey")))
lettres <- cld_result$mcletters$Letters

# Ajouter les lettres au résumé
resume_exp2 <- exp2 %>%
  group_by(regime, densite) %>%
  summarise(
    moyenne = mean(gain_poids),
    ecart_type = sd(gain_poids),
    .groups = 'drop'
  )

resume_exp2$combinaison <- paste(resume_exp2$regime, resume_exp2$densite, sep = "_")
resume_exp2$lettres <- lettres[resume_exp2$combinaison]

# Graphique avec lettres
ggplot(resume_exp2, aes(x = regime, y = moyenne, fill = densite)) +
  geom_bar(stat = "identity", position = position_dodge(0.9), alpha = 0.7) +
  geom_errorbar(aes(ymin = moyenne - ecart_type, ymax = moyenne + ecart_type),
                position = position_dodge(0.9), width = 0.25) +
  geom_text(aes(label = lettres, y = moyenne + ecart_type + 5),
            position = position_dodge(0.9), size = 5, fontface = "bold") +
  scale_fill_manual(values = c("Faible" = "#4DAF4A", "Elevee" = "#E41A1C"),
                    labels = c("Faible", "Élevée")) +
  labs(title = "Gain de poids selon le régime et la densité",
       subtitle = "Les lettres différentes indiquent des différences significatives (Tukey HSD, α=0.05)",
       x = "Régime alimentaire",
       y = "Gain de poids moyen (g)",
       fill = "Densité d'élevage") +
  theme_minimal() +
  theme(plot.title = element_text(face = "bold"))
```

**Méthode 2 : Attribution manuelle des lettres (si multcomp ne fonctionne pas)**

```r
# Après avoir réalisé les tests de Tukey (Question 4.12)
# Observer les résultats et attribuer les lettres manuellement

# Exemple de résultats attendus :
# B_Faible : meilleur groupe → lettre "a"
# C_Faible : intermédiaire → lettre "b"
# B_Elevee : intermédiaire → lettre "bc"
# A_Faible : plus faible → lettre "c"
# C_Elevee : plus faible → lettre "cd"
# A_Elevee : plus faible → lettre "d"

# Créer le vecteur de lettres manuellement
resume_exp2$lettres_manuel <- c("c", "d", "a", "bc", "b", "cd")

# Graphique
ggplot(resume_exp2, aes(x = regime, y = moyenne, fill = densite)) +
  geom_bar(stat = "identity", position = position_dodge(0.9), alpha = 0.7) +
  geom_errorbar(aes(ymin = moyenne - ecart_type, ymax = moyenne + ecart_type),
                position = position_dodge(0.9), width = 0.25) +
  geom_text(aes(label = lettres_manuel, y = moyenne + ecart_type + 5),
            position = position_dodge(0.9), size = 5, fontface = "bold") +
  scale_fill_manual(values = c("Faible" = "#4DAF4A", "Elevee" = "#E41A1C"),
                    labels = c("Faible", "Élevée")) +
  labs(title = "Gain de poids selon le régime et la densité",
       subtitle = "Les lettres identiques = groupes non significativement différents (Tukey, α=0.05)",
       x = "Régime alimentaire",
       y = "Gain de poids moyen (g)",
       fill = "Densité d'élevage") +
  theme_minimal() +
  theme(plot.title = element_text(face = "bold", size = 14),
        plot.subtitle = element_text(size = 10))
```

**Interprétation des lettres** :
- Groupes avec la **même lettre** : pas de différence significative
- Groupes avec des **lettres différentes** : différence significative
- Un groupe peut avoir **plusieurs lettres** si il est intermédiaire (ex: "bc")

---

## SYNTHÈSE ET CONCLUSIONS (à rendre)

**Question finale** : Rédigez une courte synthèse (300 mots maximum) destinée à l'aquaculteur, présentant :

1. **Expérience 1** : Quel régime alimentaire favorise le meilleur gain de poids ? Y a-t-il des différences significatives ?

2. **Expérience 2** :
   - Quel est l'effet de la densité d'élevage ?
   - L'effet du régime dépend-il de la densité (interaction) ?

3. **Recommandations pratiques** : Quelle combinaison régime-densité recommandez-vous pour maximiser la production ? Quels sont les compromis économiques à considérer (coût des aliments enrichis vs gain de poids, impact de la densité sur l'espace nécessaire) ?

**Votre synthèse :**

---

## Pour aller plus loin (Optionnel)

### 1. Analyse de puissance
Explorez l'effet de la taille des échantillons sur la puissance des tests statistiques.

```r
# Utilisez le package pwr
library(pwr)
```

### 2. ANOVA de Welch
Si l'homogénéité des variances n'est pas respectée, testez l'ANOVA de Welch (plus robuste).

```r
oneway.test(gain_poids ~ regime, data = exp1, var.equal = FALSE)
```

### 3. Transformation des données
Si les conditions ne sont pas respectées, essayez des transformations (log, racine carrée).

```r
modele_log <- lm(log(gain_poids) ~ regime, data = exp1)
```

### 4. Graphiques avancés
Créez des graphiques plus élaborés avec `ggplot2` (violon plot, raincloud plot).

### 5. ANOVA à mesures répétées
Si les mêmes truites étaient mesurées é différents temps, comment analyseriez-vous ces données ?

---

## Ressources complémentaires

- **Documentation R** :
  - `?aov`
  - `?kruskal.test`
  - `?TukeyHSD`

- **Packages utiles** :
  - `ggplot2` : visualisations
  - `dplyr` : manipulation de données
  - `FSA` : test de Dunn
  - `DescTools` : tests post-hoc
  - `agricolae` : tests post-hoc additionnels
  - `emmeans` : moyennes marginales estimées

- **Références** :
  - Zar, J.H. (2010). *Biostatistical Analysis*. 5th edition. Pearson.
  - Quinn, G.P. & Keough, M.J. (2002). *Experimental Design and Data Analysis for Biologists*. Cambridge University Press.

---

**Bon travail !**

*Ce TP a été conçu pour vous familiariser avec les principaux outils d'analyse de variance utilisés en biologie marine et aquaculture.*
