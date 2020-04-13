---
title: Comportements attachés
description: Les comportements attachés sont des classes avec une ou plusieurs propriétés attachées. Cet article montre comment créer et utiliser des comportements attachés.
ms.prod: xamarin
ms.assetid: ECEE6AEC-44FA-4AF7-BAD0-88C6EE48422E
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 04/06/2016
ms.openlocfilehash: 7d16eef4fe5422fb5cf3c039c66d1b0f113727fd
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "77131118"
---
# <a name="attached-behaviors"></a>Comportements attachés

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)

_Les comportements attachés sont des classes statiques avec une ou plusieurs propriétés attachées. Cet article montre comment créer et consommer des comportements attachés._

## <a name="overview"></a>Vue d’ensemble

Les propriétés attachées constituent un type particulier de propriété liable. Elles sont définies dans une seule classe, mais attachées à d’autres objets. En XAML, elles sont reconnaissables car elles sont représentées sous forme d’attributs contenant un nom de classe et un nom de propriété séparés par un point.

Une propriété attachée peut définir un délégué `propertyChanged` qui est exécuté quand la valeur de la propriété change, par exemple quand la propriété est définie sur un contrôle. Lorsque le délégué `propertyChanged` s’exécute, il est passé comme référence au contrôle sur lequel il est actuellement attaché, tout comme les paramètres qui contiennent les valeurs anciennes et nouvelles de la propriété. Vous pouvez utiliser ce délégué pour ajouter de nouvelles fonctionnalités au contrôle auquel la propriété est attachée en manipulant la référence qui y est passée, comme suit :

1. Le `propertyChanged` délégué jette la référence de contrôle, qui est reçue comme un [`BindableObject`](xref:Xamarin.Forms.BindableObject), au type de contrôle que le comportement est conçu pour améliorer.
1. Le délégué `propertyChanged` modifie les propriétés du contrôle, appelle les méthodes du contrôle ou inscrit les gestionnaires d’événements aux événements exposés par le contrôle, pour implémenter la fonctionnalité clé du comportement.

Le problème avec les comportements attachés est qu’ils sont définis dans une classe `static` avec des méthodes et des propriétés `static`. Il est donc difficile de créer des comportements attachés qui ont un état. De plus, les comportements Xamarin.Forms ont remplacé les comportements attachés comme approche préférée pour construire des comportements. Pour plus d’informations sur les comportements Xamarin.Forms, consultez [Comportements Xamarin.Forms](~/xamarin-forms/app-fundamentals/behaviors/creating.md) et [Comportements réutilisables](~/xamarin-forms/app-fundamentals/behaviors/reusable/index.md).

## <a name="creating-an-attached-behavior"></a>Création d’un comportement attaché

L’application d’échantillon démontre un `NumericValidationBehavior`, qui met [`Entry`](xref:Xamarin.Forms.Entry) en évidence la valeur saisie `double`par l’utilisateur dans un contrôle en rouge, si ce n’est pas un . Le comportement est illustré dans l’exemple de code suivant :

```csharp
public static class NumericValidationBehavior
{
    public static readonly BindableProperty AttachBehaviorProperty =
        BindableProperty.CreateAttached (
            "AttachBehavior",
            typeof(bool),
            typeof(NumericValidationBehavior),
            false,
            propertyChanged:OnAttachBehaviorChanged);

    public static bool GetAttachBehavior (BindableObject view)
    {
        return (bool)view.GetValue (AttachBehaviorProperty);
    }

    public static void SetAttachBehavior (BindableObject view, bool value)
    {
        view.SetValue (AttachBehaviorProperty, value);
    }

    static void OnAttachBehaviorChanged (BindableObject view, object oldValue, object newValue)
    {
        var entry = view as Entry;
        if (entry == null) {
            return;
        }

        bool attachBehavior = (bool)newValue;
        if (attachBehavior) {
            entry.TextChanged += OnEntryTextChanged;
        } else {
            entry.TextChanged -= OnEntryTextChanged;
        }
    }

    static void OnEntryTextChanged (object sender, TextChangedEventArgs args)
    {
        double result;
        bool isValid = double.TryParse (args.NewTextValue, out result);
        ((Entry)sender).TextColor = isValid ? Color.Default : Color.Red;
    }
}
```

La classe `NumericValidationBehavior` contient une propriété attachée nommée `AttachBehavior` avec une méthode getter ou setter `static`, qui contrôle l’ajout ou la suppression du comportement dans le contrôle auquel il est attaché. Cette propriété jointe inscrit la méthode `OnAttachBehaviorChanged` qui est exécutée au changement de la valeur de la propriété. Cette méthode enregistre ou dé-enregistre un [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) gestionnaire d’événements pour `AttachBehavior` l’événement, en fonction de la valeur de la propriété ci-jointe. La fonctionnalité de base du comportement `OnEntryTextChanged` est fournie par la méthode, [`Entry`](xref:Xamarin.Forms.Entry) qui analyse la `TextColor` valeur saisie dans le par `double`l’utilisateur, et définit la propriété au rouge si la valeur n’est pas un .

## <a name="consuming-an-attached-behavior"></a>Consommation d’un comportement attaché

La `NumericValidationBehavior` classe peut être consommée en ajoutant la `AttachBehavior` propriété ci-jointe à un [`Entry`](xref:Xamarin.Forms.Entry) contrôle, comme le démontre l’exemple de code XAML suivant :

```xaml
<ContentPage ... xmlns:local="clr-namespace:WorkingWithBehaviors;assembly=WorkingWithBehaviors" ...>
    ...
    <Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="true" />
    ...
</ContentPage>
```

L’équivalent [`Entry`](xref:Xamarin.Forms.Entry) dans le C est indiqué dans l’exemple de code suivant :

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, true);
```

Lors de l’exécution, le comportement répond à l’interaction avec le contrôle en fonction de l’implémentation de comportement. Les captures d’écran suivantes illustrent le comportement attaché répondant à une entrée non valide :

[![](attached-images/screenshots-sml.png "Sample Application with Attached Behavior")](attached-images/screenshots.png#lightbox "Sample Application with Attached Behavior")

> [!NOTE]
> Les comportements attachés sont écrits pour un type de contrôle spécifique (ou une superclasse qui peut s’appliquer à de nombreux contrôles) et doivent être ajoutés à un contrôle compatible uniquement. La tentative d’attachement d’un comportement à un contrôle incompatible entraîne un comportement inconnu et dépend de l’implémentation de comportement.

### <a name="removing-an-attached-behavior-from-a-control"></a>Suppression d’un comportement attaché d’un contrôle

Vous pouvez supprimer la classe `NumericValidationBehavior` d’un contrôle en définissant la propriété attachée `AttachBehavior` sur `false`, comme illustré dans l’exemple de code XAML suivant :

```xaml
<Entry Placeholder="Enter a System.Double" local:NumericValidationBehavior.AttachBehavior="false" />
```

L’équivalent [`Entry`](xref:Xamarin.Forms.Entry) dans le C est indiqué dans l’exemple de code suivant :

```csharp
var entry = new Entry { Placeholder = "Enter a System.Double" };
NumericValidationBehavior.SetAttachBehavior (entry, false);
```

Lors de l’exécution, la méthode `OnAttachBehaviorChanged` est exécutée quand la valeur de la propriété attachée `AttachBehavior` est définie sur `false`. La `OnAttachBehaviorChanged` méthode va ensuite désenregistrer [`TextChanged`](xref:Xamarin.Forms.InputView.TextChanged) le gestionnaire de l’événement pour l’événement, en veillant à ce que le comportement n’est pas exécuté que l’utilisateur interagit avec le contrôle.

## <a name="summary"></a>Récapitulatif

Cet article a montré comment créer et consommer des comportements attachés. Les comportements attachés sont des classes `static` avec une ou plusieurs propriétés attachées.

## <a name="related-links"></a>Liens connexes

- [Comportements attachés (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/behaviors-attachednumericvalidationbehavior)
