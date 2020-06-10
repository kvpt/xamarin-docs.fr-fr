---
title : « pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ? »
ms. topic : Troubleshooting ms. Prod : xamarin ms. AssetID : cab2eefb-c52f-4d81-866e-8f1feabbdd64 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 04/25/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="why-doesnt-the-visual-studio-xaml-designer-work-for-xamarinforms-xaml-files"></a>Pourquoi le concepteur XAML Visual Studio ne fonctionne-t-il pas pour les Xamarin.Forms fichiers XAML ?

Xamarin.Formsne prend actuellement pas en charge les concepteurs visuels pour les fichiers XAML. Pour cette raison, lorsque vous tentez d’ouvrir un fichier de formulaires XAML dans le *Concepteur d’interface utilisateur XAML* de Visual Studio ou dans le *Concepteur d’interface utilisateur XAML avec encodage*, le message d’erreur suivant est généré :

> «Impossible d’ouvrir le fichier avec l’éditeur sélectionné. Choisissez un autre éditeur.»

Cette limitation est décrite dans le Guide de [ Xamarin.Forms base du langage XAML](~/xamarin-forms/xaml/xaml-basics/index.md) :

> « Il n’existe pas encore de concepteur visuel pour la génération de code XAML dans Xamarin.Forms les applications. par conséquent, tout le code XAML doit être écrit manuellement ».

Toutefois, le Xamarin.Forms Générateur d’aperçu XAML peut être affiché en sélectionnant l’option **Afficher > autre Xamarin.Forms >** du générateur d’aperçu Windows.
