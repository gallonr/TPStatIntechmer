# Tutoriels R Markdown et Quarto

Ce dossier contient deux tutoriels complets pour apprendre à créer des rapports scientifiques reproductibles en aquaculture et halieutique.

## 📚 Tutoriels disponibles

### 1. Tutoriel R Markdown (`Tutoriel_RMarkdown.html`)
Guide complet sur R Markdown avec des exemples en aquaculture :
- Mise en forme du texte (titres, listes, tableaux)
- Formules mathématiques (SGR, FCR, TGC)
- Chunks de code R et leurs options
- Création de graphiques avec ggplot2
- Génération de rapports HTML, PDF et Word

**Exemples traités :** Croissance de la daurade royale, analyse du bar européen

### 2. Tutoriel Quarto (`Tutoriel_Quarto.html`)
Guide complet sur Quarto (successeur moderne de R Markdown) avec des exemples en halieutique :
- Toutes les fonctionnalités de R Markdown
- Callouts (notes, avertissements, astuces)
- Références croisées de figures et tableaux
- Layout avancé (colonnes, panneaux)
- Formules spécifiques à l'halieutique (von Bertalanffy, mortalité)

**Exemples traités :** Évaluation de stocks de poissons, captures de thon rouge

## 🚀 Pour commencer

### Prérequis
1. **R** (version 4.0 ou supérieure) - [Télécharger R](https://cran.r-project.org/)
2. **RStudio** (recommandé) - [Télécharger RStudio](https://posit.co/download/rstudio-desktop/)
3. Pour R Markdown : installer le package `rmarkdown`
   ```r
   install.packages("rmarkdown")
   ```
4. Pour Quarto : installer Quarto CLI - [Télécharger Quarto](https://quarto.org/docs/get-started/)

### Packages R nécessaires
```r
install.packages(c(
  "rmarkdown",
  "knitr",
  "ggplot2",
  "dplyr",
  "tidyr",
  "corrplot",
  "car",
  "lmtest"
))
```

## 📖 Utilisation

### Avec RStudio

#### Pour le tutoriel R Markdown :
1. Ouvrir `Tutoriel_RMarkdown.Rmd` dans RStudio
2. Cliquer sur le bouton **Knit** (ou Ctrl+Shift+K)
3. Choisir le format de sortie : HTML, PDF ou Word

#### Pour le tutoriel Quarto :
1. Ouvrir `Tutoriel_Quarto.qmd` dans RStudio
2. Cliquer sur le bouton **Render** (ou Ctrl+Shift+K)
3. Le document HTML sera généré automatiquement

### En ligne de commande

#### R Markdown :
```bash
Rscript -e 'rmarkdown::render("Tutoriel_RMarkdown.Rmd")'
```

#### Quarto :
```bash
quarto render Tutoriel_Quarto.qmd
```

## 🎓 Ce que vous apprendrez

### Mise en forme
- Titres et sous-titres (# ## ###)
- Texte en **gras**, *italique*, ***gras-italique***
- Listes à puces et numérotées
- Tableaux formatés

### Formules mathématiques
- Inline : `$formule$`
- Display : `$$formule$$`
- Exemples aquaculture : SGR, FCR, TGC
- Exemples halieutique : von Bertalanffy, mortalité

### Code R
- Création de chunks : ` ```{r} `
- Options des chunks : `echo`, `eval`, `warning`, `message`, `fig.width`, etc.
- Chunks nommés pour les références
- Chunks avec label hashtag (Quarto) : `#| label: fig-plot`

### Graphiques
- Intégration de graphiques ggplot2
- Contrôle de la taille des figures
- Légendes et références croisées
- Export en haute résolution

### Tableaux
- Tableaux markdown simples
- Tableaux avec `knitr::kable()`
- Mise en forme avancée

### Structure de document
- Table des matières automatique
- Numérotation des sections
- Thèmes et mise en page personnalisés

## 📊 Formats de sortie

### R Markdown
- **HTML** : format web interactif (par défaut)
- **PDF** : nécessite LaTeX ([TinyTeX](https://yihui.org/tinytex/) recommandé)
- **Word** : document .docx éditable

### Quarto
- **HTML** : format web moderne avec fonctionnalités avancées
- **PDF** : via LaTeX
- **Word** : document .docx
- **RevealJS** : présentations HTML interactives

## 💡 Conseils

1. **Commencez par R Markdown** si vous débutez
2. **Passez à Quarto** pour bénéficier de fonctionnalités modernes
3. **Utilisez des chunks nommés** pour faciliter le débogage
4. **Testez régulièrement** en compilant votre document
5. **Consultez la documentation officielle** :
   - [R Markdown](https://rmarkdown.rstudio.com/)
   - [Quarto](https://quarto.org/)

## 📝 Licence

Ces tutoriels sont fournis à des fins pédagogiques dans le cadre du cours de statistiques en aquaculture et halieutique.
