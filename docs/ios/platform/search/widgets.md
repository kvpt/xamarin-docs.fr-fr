---
title: Améliorations des widgets de recherche et d’écran d’accueil dans iOS 10
description: Ce document décrit les améliorations apportées par Apple aux widgets dans iOS 10, y compris les mises à jour des widgets de recherche et d’écran d’accueil.
ms.prod: xamarin
ms.assetid: D66FD9E1-9E23-4BB6-825C-ED19B8F72A81
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 03/17/2017
ms.openlocfilehash: 9a45fcccd693a51230a51600964acd75704e1b93
ms.sourcegitcommit: 00e6a61eb82ad5b0dd323d48d483a74bedd814f2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91429718"
---
# <a name="search-and-home-screen-widget-enhancements-in-ios-10"></a>Améliorations des widgets de recherche et d’écran d’accueil dans iOS 10

_Cet article aborde les améliorations apportées par Apple au système widget dans iOS 10._

Apple a introduit plusieurs améliorations du système widget pour s’assurer que les widgets s’affichent parfaitement sur n’importe quel arrière-plan existant sur le nouvel écran de verrouillage iOS 10. En outre, les widgets contiennent désormais une propriété [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) qui permet au développeur de décrire la quantité de contenu disponible et permet à l’utilisateur de développer et de réduire le contenu.

Les widgets (également appelés « extensions aujourd’hui ») sont un type spécial d’extension iOS qui affiche une petite quantité d’informations utiles ou qui exposent les fonctionnalités spécifiques à l’application dans les délais impartis. Par exemple, l’application de News contient un widget qui affiche les principaux titres et l’application de calendrier fournit deux widgets différents : un pour afficher les événements du jour et un pour afficher les événements à venir.

Les widgets sont hautement personnalisables et peuvent contenir des éléments d’interface utilisateur tels que du texte, des images, des boutons, etc. En outre, le développeur peut personnaliser davantage la disposition de ses widgets.

[![Exemples de widgets](widgets-images/widgets01.png)](widgets-images/widgets01.png#lightbox)

Il existe deux emplacements principaux qu’un utilisateur peut afficher et interagir avec les widgets d’une application :

- **Écran de recherche** : les utilisateurs peuvent ajouter les widgets qu’ils trouvent les plus utiles à leur écran de recherche. L’écran de recherche est accessible en effectuant un balayage à droite sur les écrans Accueil et verrouillage.
- **Écran d’accueil** -à partir de l’écran d’accueil, l’utilisateur peut utiliser la touche d’activation en 3D pour ouvrir la liste actions rapides en appliquant une pression à l’icône de l’application. Les widgets d’une application s’affichent au-dessus de la liste d’actions rapides. Pour plus d’informations, consultez notre [Introduction à la documentation 3D touch](~/ios/platform/3d-touch.md) .

## <a name="widgets-developer-suggestions"></a>Suggestions pour les développeurs de widgets

Dans l’idéal, le développeur doit toujours essayer et concevoir des widgets que l’utilisateur souhaite ajouter à leurs écrans de recherche. À cette fin, Apple présente les suggestions suivantes :

- **Créez une expérience remarquable et agréable** : les widgets de l’utilisateur souhaitent obtenir des informations succinctes et rapides sur les mises à jour d’État ou les autoriser à effectuer rapidement des tâches simples. Cela permet de fournir la quantité d’informations et l’interactivité adéquates. Dans la mesure du possible, autorisez l’utilisateur à effectuer une tâche donnée avec un seul TAP. De plus, étant donné que les widgets ne prennent pas en charge le panoramique ou le défilement, vous devez les prendre en compte dans la conception du widget.
- **Afficher rapidement le contenu** : les widgets sont conçus pour être plus rapides, de sorte que l’utilisateur ne doit jamais attendre le chargement du contenu une fois qu’un widget est affiché. Les widgets doivent mettre en cache leur contenu localement afin qu’ils puissent afficher le contenu récent pendant le chargement du contenu actualisé en arrière-plan.
- **Fournir le remplissage et les marges appropriés** -les widgets ne doivent jamais paraître encombrés, donc évitez d’étendre le contenu aux bords de la vue d’un widget. Il doit toujours y avoir une marge d’étendue de plusieurs pixels entre les bords et le contenu. Apple suggère également l’utilisation de l’icône de l’application, affichée en haut du widget, sous la forme d’un guide d’alignement. Si le widget présente une disposition en grille, assurez-vous qu’il existe un remplissage correct entre les éléments de la grille et essayez de limiter le nombre d’éléments à quatre.
- **Utiliser des dispositions adaptables** : la largeur d’un widget varie en fonction de l’appareil sur lequel il s’exécute et de l’orientation de l’appareil. La hauteur d’un widget peut également varier en fonction de s’il est affiché dans un État réduit (par défaut) ou développé (non pris en charge par tous les widgets). Un widget réduit a une hauteur d’environ deux lignes de table iOS standard et demie. Le développeur peut demander la taille d’un widget développé, mais il doit idéalement être inférieur à la hauteur de l’écran. Dans l’État réduit, le widget doit afficher uniquement les informations essentielles et autonomes. Lorsqu’il est développé, le widget doit afficher des informations supplémentaires qui améliorent le contenu principal affiché dans l’État réduit. Les widgets affichés dans la liste d’actions rapides ne seront que dans l’État réduit.
- **Ne personnalisez pas l’arrière-plan du widget** . les widgets sont affichés sur un arrière-plan clair et flou fourni par le système. Cela permet de promouvoir la cohérence entre les widgets et d’améliorer la lisibilité de leur contenu. Évitez d’utiliser une image comme arrière-plan du widget, car elle risque d’entrer en conflit avec les papiers peints du verrou et de l’écran d’accueil de l’utilisateur.
- **Utilisez la police système en noir ou gris foncé** -lorsque vous affichez du texte dans un widget, la police système fonctionne mieux. La police doit être dans une couleur grise noire ou foncée pour se mettre en évidence par rapport à l’arrière-plan du widget clair et flou.
- **Fournir l’accès à l’application lorsque** le widget doit toujours fonctionner séparément de son application. Toutefois, si des fonctionnalités plus approfondies sont requises, le widget doit être en mesure de lancer l’application pour afficher ou modifier une information spécifique. N’incluez jamais de bouton « ouvrir une application », autorisez simplement l’utilisateur à appuyer sur le contenu lui-même et à ne jamais ouvrir une application tierce.
- **Sélectionner un nom de widget clair et concis** : l’icône de l’application et le nom du widget sont toujours affichés sur le contenu du widget. Apple suggère d’utiliser le nom de l’application pour son widget principal et un nom clair et concis pour tous les autres éléments qu’il fournit. Lorsque vous fournissez un titre de widget personnalisé, ceux-ci doivent être précédés du nom de l’application (par exemple, Maps à proximité, cartes de restaurants, etc.).
- **Informer lorsque l’authentification ajoute** de la valeur : si des fonctionnalités ou des informations supplémentaires sont disponibles uniquement lorsque l’utilisateur est authentifié et connecté, présentez-le à l’utilisateur. Par exemple, une application de partage des adresses peut indiquer « se connecter à la signature de livre ».
- **Sélectionner un widget de liste d’actions rapides** : si l’application fournit plusieurs widgets, le développeur doit choisir celui qui doit être présent lorsque l’utilisateur fait apparaître la liste d’actions rapides en appliquant une pression à l’icône de l’application à l’aide de la fonction tactile 3D.

Pour plus d’informations sur l’utilisation des widgets, consultez notre [Introduction aux extensions](~/ios/platform/extensions.md), [Présentation de la documentation tactile 3D](~/ios/platform/3d-touch.md) et [Guide de programmation](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)de l’extension d’application d’Apple.

## <a name="working-with-vibrancy"></a>Utilisation du vibreur

Vibrance garantit que le texte d’un widget reste lisible lorsqu’il est présenté sur la lumière du widget, en arrière-plan flou (fourni par le système). Avant iOS 10, le développeur utiliserait un [NotificationCenterVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1613917-notificationcentervibrancyeffect) pour la Vibrance du widget. Par exemple :

```csharp
// DEPRECATED: Get Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreateForNotificationCenter ();
```

Cette valeur est déconseillée dans iOS 10 et doit être remplacée par un [WidgetPrimaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771278-widgetprimaryvibrancyeffect) ou un [WidgetSecondaryVibrancyEffect](https://developer.apple.com/reference/uikit/uivibrancyeffect/1771277-widgetsecondaryvibrancyeffect). Par exemple :

```csharp
// Get Primary Widget Vibrancy Effect
var vibrancy = UIVibrancyEffect.CreatePrimaryVibrancyEffectForNotificationCenter ();

// Get Secondary Widget Vibrancy Effect
var vibrancy2 = UIVibrancyEffect.CreateSecondaryVibrancyEffectForNotificationCenter ();
```

## <a name="working-with-collapsed-and-expanded-widgets"></a>Utilisation des widgets réduits et développés

Nouveauté d’iOS 10, les widgets contiennent désormais une propriété [NCWidgetDisplayMode](https://developer.apple.com/reference/notificationcenter/ncwidgetdisplaymode) qui permet au développeur de décrire la quantité de contenu disponible et permet à l’utilisateur de développer et de réduire le contenu.

Lorsqu’un widget est initialement affiché, il est dans un État réduit. Un widget réduit a une hauteur d’environ deux lignes de table iOS standard et demie. Le développeur peut demander la taille d’un widget développé, mais il doit idéalement être inférieur à la hauteur de l’écran.

Dans l’État réduit, le widget doit afficher uniquement les informations essentielles et autonomes. Lorsqu’il est développé, le widget doit afficher des informations supplémentaires qui améliorent le contenu principal affiché dans l’État réduit. Par exemple, l’application météo affiche les conditions météorologiques actuelles lorsqu’elle est réduite, et ajoute les prévisions horaires lorsqu’elles sont développées.

Les widgets affichés dans la liste d’actions rapides ne seront que dans l’État réduit. Si l’application fournit plusieurs widgets, le développeur doit choisir celui qui doit être présent lorsque l’utilisateur affiche la liste d’actions rapides en appliquant une pression à l’icône de l’application à l’aide de l’interface tactile 3D.

L’exemple suivant est une simple extension Today (widget) qui gère les États réduits et développés :

```csharp
using System;
using NotificationCenter;
using Foundation;
using UIKit;
using CoreGraphics;

namespace MonkeyAbout
{
    public partial class TodayViewController : UIViewController, INCWidgetProviding
    {
        protected TodayViewController (IntPtr handle) : base (handle)
        {
            // Note: this .ctor should not contain any initialization logic.
        }

        public override void ViewDidLoad ()
        {
            base.ViewDidLoad ();

            // Tell widget it can be expanded
            ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);

            // Get the maximum size
            var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
        }

        [Export ("widgetPerformUpdateWithCompletionHandler:")]
        public void WidgetPerformUpdate (Action<NCUpdateResult> completionHandler)
        {
            // Take action based on the display mode
            switch (ExtensionContext.GetWidgetActiveDisplayMode()) {
            case NCWidgetDisplayMode.Compact:
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                Content.Text = "Gorilla!!!!";
                break;
            }

            // Report results
            // If an error is encoutered, use NCUpdateResultFailed
            // If there's no update required, use NCUpdateResultNoData
            // If there's an update, use NCUpdateResultNewData
            completionHandler (NCUpdateResult.NewData);
        }

        [Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
        public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
        {
            // Take action based on the display mode
            switch (activeDisplayMode) {
            case NCWidgetDisplayMode.Compact:
                PreferredContentSize = maxSize;
                Content.Text = "Let's Monkey About!";
                break;
            case NCWidgetDisplayMode.Expanded:
                PreferredContentSize = new CGSize (0, 200);
                Content.Text = "Gorilla!!!!";
                break;
            }
        }

    }
}
```

Examinez en détail le code spécifique du mode d’affichage du widget. Pour informer le système que ce widget prend en charge l’état développé, il utilise :

```csharp
// Tell widget it can be expanded
ExtensionContext.SetWidgetLargestAvailableDisplayMode (NCWidgetDisplayMode.Expanded);
```

Pour obtenir le mode d’affichage actuel du widget, il utilise :

```csharp
ExtensionContext.GetWidgetActiveDisplayMode()
```

Pour obtenir la taille maximale d’un État réduit ou développé, il utilise :

```csharp
// Get the maximum size
var maxSize = ExtensionContext.GetWidgetMaximumSize (NCWidgetDisplayMode.Expanded);
```

Et pour gérer le changement d’État (mode d’affichage), il utilise :

```csharp
[Export ("widgetActiveDisplayModeDidChange:withMaximumSize:")]
public void WidgetActiveDisplayModeDidChange (NCWidgetDisplayMode activeDisplayMode, CGSize maxSize)
{
    // Take action based on the display mode
    switch (activeDisplayMode) {
    case NCWidgetDisplayMode.Compact:
        PreferredContentSize = maxSize;
        Content.Text = "Let's Monkey About!";
        break;
    case NCWidgetDisplayMode.Expanded:
        PreferredContentSize = new CGSize (0, 200);
        Content.Text = "Gorilla!!!!";
        break;
    }
}
```

En plus de définir la taille demandée pour chaque État (réduit ou développé), il met également à jour le contenu affiché pour qu’il corresponde à la nouvelle taille.

## <a name="summary"></a>Récapitulatif

Cet article a abordé les améliorations apportées par Apple au système du widget dans iOS 10 et expliqué comment les implémenter dans Xamarin. iOS.

## <a name="related-links"></a>Liens associés

- [Exemples iOS 10](/samples/browse/?products=xamarin&term=Xamarin.iOS%2biOS10)
- [Présentation des extensions](~/ios/platform/extensions.md)
- [Présentation de l’interface tactile 3D](~/ios/platform/3d-touch.md)
- [Guide de programmation de l’extension d’application](https://developer.apple.com/library/prerelease/content/documentation/General/Conceptual/ExtensibilityPG/index.html)