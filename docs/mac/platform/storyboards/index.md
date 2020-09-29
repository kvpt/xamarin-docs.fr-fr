---
title: Présentation des storyboards dans Xamarin. Mac
description: Cet article fournit une introduction à l’utilisation des storyboards dans une application Xamarin. Mac. Il aborde la création et la gestion de l’interface utilisateur de l’application à l’aide de storyboards et d’Interface Builder de Xcode.
ms.prod: xamarin
ms.assetid: F37BA503-0B25-489F-80A8-58C493291A55
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 03/14/2017
ms.openlocfilehash: 24a1e1af5b56a3cd10557658a0cad0c4e73ebac8
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91433367"
---
# <a name="introduction-to-storyboards-in-xamarinmac"></a>Présentation des storyboards dans Xamarin. Mac

_Cet article fournit une introduction à l’utilisation des storyboards dans une application Xamarin. Mac. Il aborde la création et la gestion de l’interface utilisateur de l’application à l’aide des storyboards et des Interface Builder de Xcode._

Les storyboards vous permettent de développer une interface utilisateur pour votre application Xamarin. Mac qui inclut non seulement les définitions et les contrôles de fenêtres, mais également les liens entre les différentes fenêtres (via SEGUES) et les États d’affichage.

[![Exemple d’interface utilisateur dans Xcode](images/intro01.png)](images/intro01.png#lightbox)

Cet article fournit une introduction à l’utilisation des storyboards pour définir une interface utilisateur de l’application Xamarin. Mac.

<a name="What-are-Storyboards"></a>

## <a name="what-are-storyboards"></a>Que sont les storyboards ?

En utilisant des storyboards, toute l’interface utilisateur d’une application Xamarin. Mac peut être définie dans un emplacement unique avec l’ensemble de la navigation entre ses éléments individuels et les interfaces utilisateur. Les storyboards pour Xamarin. Mac, fonctionnent de manière très similaire aux storyboards pour Xamarin. iOS. Toutefois, ils contiennent un ensemble différent de _types segue_ en raison des différents idiomes d’interface.

<a name="Working-with-Scenes"></a>

### <a name="working-with-scenes"></a>Utilisation des scènes

Comme indiqué ci-dessus, un Storyboard définit la totalité de l’interface utilisateur pour une application donnée, décomposée en une vue d’ensemble fonctionnelle de ses _contrôleurs d’affichage_. Dans Interface Builder de Xcode, chacun de ces contrôleurs vit dans sa propre _scène_.

[![Exemple de contrôleur d’affichage](images/intro02.png)](images/intro02.png#lightbox)

Chaque scène représente une vue donnée et une paire de contrôleurs d’affichage avec un ensemble de lignes (appelé SEGUES) qui connectent chaque scène de l’interface utilisateur, ce qui affiche leurs relations. Certains SEGUES définissent la façon dont un contrôleur d’affichage contient un ou plusieurs affichages enfants ou contrôleurs d’affichage. D’autres SEGUES, définissent des transitions entre le contrôleur d’affichage (comme l’affichage d’une boîte de dialogue Menu segue ou). 

[![Exemple de Segue](images/intro03.png)](images/intro03.png#lightbox)

La chose la plus importante à noter est que chaque segue représente le workflow d’une forme de données entre l’élément donné de l’interface utilisateur de l’application.

<a name="Working-with-View-Controllers"></a>

### <a name="working-with-view-controllers"></a>Utilisation des contrôleurs d’affichage

Les contrôleurs d’affichage définissent les relations entre une vue donnée des informations au sein d’une application Mac et le modèle de données qui fournit ces informations. Chaque scène de niveau supérieur dans le Storyboard représente un contrôleur d’affichage dans le code de l’application Xamarin. Mac.

[![Exemple de contrôleur d’affichage de feuillets](images/intro04.png)](images/intro04.png#lightbox)

De cette façon, chaque contrôleur d’affichage est un jumelage autonome et réutilisable de la représentation visuelle des informations (vue) et de la logique de présentation et de contrôle de ces informations.

Dans une scène donnée, vous pouvez effectuer toutes les opérations qui auraient normalement été traitées par des fichiers individuels `.xib` : 

- Placez des sous-vues et des contrôles (tels que des boutons et des zones de texte).
- Définissez les positions des éléments et les contraintes de disposition automatique.
- Actions et débouchés de mise en réseau pour exposer des éléments d’interface utilisateur au code.

<a name="Working-with-Segues"></a>

### <a name="working-with-segues"></a>Utilisation de SEGUES

Comme indiqué ci-dessus, SEGUES fournit les relations entre toutes les scènes qui définissent l’interface utilisateur de votre application. Si vous êtes familiarisé avec l’utilisation des storyboards dans iOS, vous savez que SEGUES pour iOS définit généralement des transitions entre les vues plein écran. Cela diffère de macOS, lorsque SEGUES définit généralement la « relation contenant-contenu » (où une scène est l’enfant d’une scène parente).

Dans macOS, la plupart des applications ont tendance à regrouper leurs vues dans la même fenêtre à l’aide d’éléments d’interface utilisateur tels que les onglets et les affichages fractionnés. Contrairement à iOS, où les vues doivent être activées et désactivées à l’écran, en raison d’un espace d’affichage physique limité.

En raison de l’banlieusards de Mac pour la relation contenant-contenu, il existe des situations où les _SEGUES de présentation_ sont utilisés, tels que les fenêtres modales, les vues de feuille et les popovers.

Lorsque vous utilisez la SEGUES de présentation, vous pouvez remplacer la `PrepareForSegue` méthode du contrôleur d’affichage parent pour la présentation pour initialiser et les variables et fournir toutes les données au contrôleur d’affichage en cours de présentation.

<a name="Design-and-Run-Times"></a>

### <a name="design-and-run-times"></a>Temps de conception et d’exécution

Au moment de la conception (lors de la disposition de l’interface utilisateur dans l’Interface Builder de Xcode), chaque élément de l’interface utilisateur de l’application est divisé en éléments constitutifs :

- **Scènes** , composées des éléments suivants :
  - **Contrôleur d’affichage** : qui définissent les relations entre les vues et les données qui les prennent en charge.
  - **Vues et** sous-vues-éléments réels qui composent l’interface utilisateur.
  - **SEGUES de relation contenant-contenu** : qui définissent les relations parent-enfant entre les scènes.
- **SEGUES de présentation** : qui définissent des modes de présentation individuels. 

En définissant chaque élément de cette manière, il permet le chargement différé de chaque élément uniquement si nécessaire pendant l’exécution. Dans macOS, l’ensemble du processus a été conçu pour permettre au développeur de créer des interfaces utilisateur complexes et flexibles qui nécessitent un minimum de code de sauvegarde pour les faire fonctionner, tout en étant aussi efficaces que possible avec les ressources système.

<a name="Storyboard-Quick-Start"></a>

## <a name="storyboard-quick-start"></a>Démarrage rapide de la table de montage séquentiel

Dans le Guide de Démarrage rapide de la [table de montage séquentiel](~/mac/platform/storyboards/quickstart.md) , nous allons créer une simple application Xamarin. Mac qui présente les concepts clés de l’utilisation des storyboards pour créer une interface utilisateur. L’exemple d’application se compose d’une vue Spilt contenant une _zone de contenu_ et d’une zone d' _inspecteur_ . elle présente une fenêtre de boîte de dialogue de préférences simple. Nous allons utiliser SEGUES pour lier tous les éléments de l’interface utilisateur.

<a name="Working-with-Storyboards"></a>

## <a name="working-with-storyboards"></a>Utilisation de storyboards

Cette section décrit en détail l' [utilisation des storyboards](~/mac/platform/storyboards/indepth.md) dans une application Xamarin. Mac. Nous examinons en détail les scènes et la façon dont elles se composent de contrôleurs d’affichage et d’une vue. Ensuite, nous allons examiner comment les scènes sont liées à SEGUES. Enfin, nous examinerons l’utilisation des types de Segue personnalisés. 

<a name="Complex-Storyboard-Example"></a>

## <a name="complex-storyboard-example"></a>Exemple de Storyboard complexe

Pour obtenir un exemple d’utilisation complexe des storyboards dans une application Xamarin. Mac, consultez l' [exemple d’application SourceWriter](/samples/xamarin/mac-samples/sourcewriter). SourceWriter est un éditeur de code source simple qui assure la prise en charge de la complétion de code et de la coloration syntaxique de base.

Le code SourceWriter a été entièrement commenté et, le cas échéant, des liens ont être fournis entre les principales technologies ou méthodes et des informations pertinentes dans la documentation des guides Xamarin.Mac.

<a name="Summary"></a>

## <a name="summary"></a>Récapitulatif

Cet article a fait un aperçu rapide de l’utilisation des storyboards dans une application Xamarin. Mac. Nous avons vu comment créer une nouvelle application à l’aide de storyboards et comment définir une interface utilisateur. Nous avons également vu comment naviguer entre les différentes fenêtres et les États d’affichage à l’aide de SEGUES.

## <a name="related-links"></a>Liens associés

- [Hello, Mac (exemple)](/samples/xamarin/mac-samples/hello-mac)
- [Hello, Mac](~/mac/get-started/hello-mac.md)
- [Utilisation de Windows](~/mac/user-interface/window.md)
- [OS X Human Interface Guidelines](https://developer.apple.com/library/mac/documentation/UserExperience/Conceptual/OSXHIGuidelines/)
- [Présentation de Windows](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/WinPanel/Introduction.html#//apple_ref/doc/uid/10000031-SW1)