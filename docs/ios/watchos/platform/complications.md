---
title: les complications Watchos dans Xamarin
description: Ce document décrit comment utiliser les complications Watchos dans Xamarin. Il explique comment ajouter une complication, écrire une complication, des modèles et fournit des exemples de code.
ms.prod: xamarin
ms.assetid: 7ACD9A2B-CF69-46EA-B0C8-10E7D81216E8
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/03/2017
ms.openlocfilehash: 7e2b3e93baaeac85267c9db2f414793610521f2e
ms.sourcegitcommit: 1e3a0d853669dcc57d5dee0894d325d40c7d8009
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/31/2019
ms.locfileid: "70200026"
---
# <a name="watchos-complications-in-xamarin"></a>les complications Watchos dans Xamarin

_Watchos permet aux développeurs d’écrire des complications personnalisées pour regarder des visages_

Cette page explique les différents types de complications disponibles et explique comment ajouter une complication à votre application Watchos 3.

Notez que chaque application Watchos ne peut avoir qu’une seule complication.

Commencez par lire les [documents d’Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ManagingComplications.html) pour déterminer si votre application est adaptée à une complication. Vous avez le `CLKComplicationFamily` choix entre cinq types d’affichage:

[![](complications-images/all-complications-sml.png "Les 5 types CLKComplicationFamily disponibles: Petit rond, petit, modulaire, grand, utilitaire petit, utilitaire grand")](complications-images/all-complications.png#lightbox)

Les applications peuvent implémenter un seul style, ou les cinq, en fonction des données affichées.
Vous pouvez également prendre en charge le temps de trajet, en fournissant des valeurs pour les heures passées et/ou futures au fur et à mesure que l’utilisateur convertit la Digital Crown.

<a name="adding" />

## <a name="adding-a-complication"></a>Ajout d’une complication

### <a name="configuration"></a>Configuration

Des complications peuvent être ajoutées à une application Watch pendant la création ou ajoutées manuellement à une solution existante.

### <a name="add-new-project"></a>Ajouter un nouveau projet...

L’Assistant **Ajouter un nouveau projet...** comprend une case à cocher qui crée automatiquement une classe du contrôleur de complication et configure le fichier **info. plist** :

![](complications-images/file-new-project-sml.png "La case à cocher inclure une complication")

### <a name="existing-projects"></a>Projets existants

Pour ajouter une complication à un projet existant:

1. Créez un fichier de classe **ComplicationController.cs** et implémentez `CLKComplicationDataSource`.
2. Configurez le fichier **info. plist** de l’application pour exposer la complication et identifier les familles de complication prises en charge.

Ces étapes sont décrites plus en détail ci-dessous.

<a name="clkcomplicationcontroller" />

### <a name="clkcomplicationdatasource-class"></a>CLKComplicationDataSource, classe

Le modèle C# suivant comprend les méthodes minimales requises pour implémenter `CLKComplicationDataSource`un.

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
  public ComplicationController ()
  {
  }
  public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
  {
  }
  public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
  {
  }
  public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
  {
  }
}
```

Suivez les instructions d' [écriture d’une complication](#writing) pour ajouter du code à cette classe.

### <a name="infoplist"></a>Info.plist

Le fichier **info. plist** de l’extension Watch doit spécifier le nom `CLKComplicationDataSource` du et les familles de complication que vous souhaitez prendre en charge:

[![](complications-images/complications-config-sml.png "Types de la famille de complication")](complications-images/complications-config.png#lightbox)

La liste d’entrée de la **classe de source de données** indique les `CLKComplicationDataSource` noms de classe qui sous-classent la sous-classe qui comprend votre logique de complication.

## <a name="clkcomplicationdatasource"></a>CLKComplicationDataSource

Toutes les fonctionnalités de complication sont implémentées dans une classe unique, en remplaçant `CLKComplicationDataSource` les méthodes de la classe abstraite `ICLKComplicationDataSource` (qui implémente l’interface).

### <a name="required-methods"></a>Méthodes requises

Vous devez implémenter les méthodes suivantes pour que la complication s’exécute:

- `GetPlaceholderTemplate`-Retourne l’affichage statique utilisé pendant la configuration ou lorsque l’application ne peut pas fournir de valeur.
- `GetCurrentTimelineEntry`-Calculer l’affichage correct lorsque la complication est en cours d’exécution.
- `GetSupportedTimeTravelDirections`- `CLKComplicationTimeTravelDirections` Retourne des options telles que `None`, `Forward` `Backward`, ou `Forward | Backward`.

### <a name="privacy"></a>Confidentialité

Complications qui affichent des données personnelles

- `GetPrivacyBehavior` - `CLKComplicationPrivacyBehavior.ShowOnLockScreen` ou `HideOnLockScreen`

Si cette méthode est `HideOnLockScreen` retournée, la complication affiche soit une icône, soit le nom de l’application (et non aucune donnée) lorsque la montre est verrouillée.

### <a name="updates"></a>Mises à jour

- `GetNextRequestedUpdateDate`-Retourner une heure à laquelle le système d’exploitation doit interroger l’application pour obtenir des informations de complication mises à jour.

Vous pouvez également forcer une mise à jour à partir de votre application iOS.

### <a name="supporting-time-travel"></a>Prise en charge des voyages

La prise en charge des voyages est facultative et contrôlée `GetSupportedTimeTravelDirections` par la méthode. Si elle retourne `Forward`, `Backward`, ou `Forward | Backward` que vous devez implémenter les méthodes suivantes

- `GetTimelineStartDate`
- `GetTimelineEndDate`
- `GetTimelineEntriesBeforeDate`
- `GetTimelineEntriesAfterDate`

<a name="writing" />

## <a name="writing-a-complication"></a>Écriture d’une complication

Les complications vont de l’affichage des données simples au rendu d’images et de données complexes avec prise en charge du temps de voyage. Le code ci-dessous montre comment créer une simple compliquation à un seul modèle.

<!--
The [sample]() for this article supports more template styles.
-->

## <a name="sample-code"></a>Exemple de code

Cet exemple ne prend en `UtilitarianLarge` charge que le modèle; par conséquent, il ne peut être sélectionné que sur des visages de vigilance spécifiques qui prennent en charge ce type de complication. Quand *vous sélectionnez* des complications sur une montre, elle affiche **ma complication** et, lors de son *exécution* , elle affiche le texte  **minute** (avec une partie de l’heure).

```csharp
[Register ("ComplicationController")]
public class ComplicationController : CLKComplicationDataSource
{
    public ComplicationController ()
    {
    }
    public ComplicationController (IntPtr p) : base (p)
    {
    }
    public override void GetCurrentTimelineEntry (CLKComplication complication, Action<CLKComplicationTimelineEntry> handler)
    {
        CLKComplicationTimelineEntry entry = null;
    var complicationDisplay = "MINUTE " + DateTime.Now.Minute.ToString(); // text to display on watch face
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge)
        {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText(complicationDisplay); // dynamic display
            entry = CLKComplicationTimelineEntry.Create(NSDate.Now, textTemplate);
        } else {
            Console.WriteLine("Complication family timeline not supported (" + complication.Family + ")");
        }
        handler (entry);
    }
    public override void GetPlaceholderTemplate (CLKComplication complication, Action<CLKComplicationTemplate> handler)
    {
        CLKComplicationTemplate template = null;
        if (complication.Family == CLKComplicationFamily.UtilitarianLarge) {
            var textTemplate = new CLKComplicationTemplateUtilitarianLargeFlat ();
            textTemplate.TextProvider = CLKSimpleTextProvider.FromText ("MY COMPLICATION"); // static display
            template = textTemplate;
        } else {
            Console.WriteLine ("Complication family placeholder not not supported (" + complication.Family + ")");
        }
        handler (template);
    }
    public override void GetSupportedTimeTravelDirections (CLKComplication complication, Action<CLKComplicationTimeTravelDirections> handler)
    {
        handler (CLKComplicationTimeTravelDirections.None);
    }
}
```


<a name="templates" />

## <a name="complication-templates"></a>Modèles de complication

Un certain nombre de modèles différents sont disponibles pour chaque type de complication.
Les modèles en **anneau** vous permettent d’afficher un anneau de style de progression autour de la complication, qui peut être utilisée pour afficher la progression ou une autre valeur graphiquement.

[Documentation CLKComplicationTemplate d’Apple](https://developer.apple.com/reference/clockkit/clkcomplicationtemplate)

### <a name="circular-small"></a>Petit rond

Ces noms de classe de `CLKComplicationTemplateCircularSmall`modèle portent tous le préfixe:

- **RingImage** : affiche une image unique, avec un anneau de progression autour.
- **RingText** : affiche une seule ligne de texte, avec un anneau de progression autour.
- **SimpleImage** : Affichez simplement une petite image unique.
- **SimpleText** : affiche simplement un petit extrait de texte.
- **StackImage** -afficher une image et une ligne de texte, l’une au-dessus de l’autre
- **StackText** : affiche deux lignes de texte.

### <a name="modular-small"></a>Petit modulaire

Ces noms de classe de `CLKComplicationTemplateModularSmall`modèle portent tous le préfixe:

- **ColumnsText** : affiche une petite grille de valeurs de texte (2 lignes et 2 colonnes).
- **RingImage** : affiche une image unique, avec un anneau de progression autour.
- **RingText** : affiche une seule ligne de texte, avec un anneau de progression autour.
- **SimpleImage** : Affichez simplement une petite image unique.
- **SimpleText** : affiche simplement un petit extrait de texte.
- **StackImage** -afficher une image et une ligne de texte, l’une au-dessus de l’autre
- **StackText** : affiche deux lignes de texte.

### <a name="modular-large"></a>Grande Modular

Ces noms de classe de `CLKComplicationTemplateModularLarge`modèle portent tous le préfixe:

- **Colonnes** -afficher une grille de 3 lignes avec 2 colonnes, en incluant éventuellement une image à gauche de chaque ligne.
- **StandardBody** : affiche une chaîne d’en-tête en gras, avec deux lignes de texte brut. L’en-tête peut éventuellement afficher une image sur la gauche.
- **Table** : affiche une chaîne d’en-tête en gras, avec une grille de texte 2x2 au-dessous. L’en-tête peut éventuellement afficher une image sur la gauche.
- **TallBody** : affiche une chaîne d’en-tête en gras avec une seule ligne de texte au-dessous.

### <a name="utilitarian-small"></a>Utilitaire petit

Ces noms de classe de `CLKComplicationTemplateUtilitarianSmall`modèle portent tous le préfixe:

- **Flat** -affiche une image et du texte sur une seule ligne (le texte doit être abrégé).
- **RingImage** : affiche une image unique, avec un anneau de progression autour.
- **RingText** : affiche une seule ligne de texte, avec un anneau de progression autour.
- **Square** -affiche une image carrée (carré 40px ou 44px pour les Apple Watch respectivement 38mm ou Watch 42mm).

### <a name="utilitarian-large"></a>Utilitaire grand

Il n’existe qu’un seul modèle pour ce style de `CLKComplicationTemplateUtilitarianLargeFlat`complication:.
Elle affiche une seule image et du texte, le tout sur une seule ligne.



## <a name="related-links"></a>Liens associés

- [Documentation d’Apple](https://developer.apple.com/library/watchos/documentation/General/Conceptual/WatchKitProgrammingGuide/ComplicationEssentials.html)
- [Vidéo WWDC](https://developer.apple.com/videos/play/wwdc2015-209/)
