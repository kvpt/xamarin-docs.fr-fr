---
title: Spécificités de la plateforme
description: Caractéristiques de la plateforme vous autorisons à utiliser les fonctionnalités qui est disponible uniquement sur une plateforme spécifique, sans avoir à implémenter des convertisseurs personnalisés ou des effets. Cet article explique comment utiliser et créer des spécifiques à la plateforme.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
ms.openlocfilehash: f6190b9c0d29d57d6d509bdff25e2ce3572e3a3c
ms.sourcegitcommit: eedc6032eb5328115cb0d99ca9c8de48be40b6fa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 03/07/2020
ms.locfileid: "78910555"
---
# <a name="platform-specifics"></a>Spécificités de la plateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés._

Le processus pour consommer un spécifique à la plateforme via XAML ou via l’API de code fluent est comme suit :

1. Ajoutez une déclaration `xmlns` ou `using` directive pour l’espace de noms [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .
1. Ajoutez une déclaration `xmlns` ou `using` directive pour l’espace de noms qui contient les fonctionnalités spécifiques à la plateforme :
    1. Sur iOS, il s’agit de l’espace de noms [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) .
    1. Sur Android, il s’agit de l’espace de noms [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) . Pour Android AppCompat, il s’agit de l’espace de noms [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) .
    1. Sur la plateforme Windows universelle, il s’agit de l’espace de noms [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) .
1. Appliquez la spécifique à la plateforme à partir de XAML, ou à partir du code avec l’API Fluent `On<T>`. La valeur de `T` peut être [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS), [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android)ou [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) types à partir de l’espace de noms [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) .

> [!NOTE]
> Notez que toute tentative consommer un spécifique à la plateforme sur une plateforme où il n’est pas disponible non entraîne une erreur. Au lieu de cela, le code s’exécute sans plateforme spécifiques à l’application.

Les caractéristiques de la plateforme consommées via l’API de code `On<T>` Fluent renvoient [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objets. Cela permet plusieurs caractéristiques de la plateforme à appeler sur le même objet avec la méthode en cascade.

Pour plus d’informations sur les spécificités de la plateforme fournies par Xamarin. Forms, consultez informations [spécifiques à](~/xamarin-forms/platform/ios/index.md)la plateforme iOS, caractéristiques [spécifiques à](~/xamarin-forms/platform/android/index.md)la plateforme Android et [spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Création de caractéristiques spécifiques à la plateforme

Les fournisseurs peuvent créer leurs propres propres plateformes avec des effets. Un effet fournit les fonctionnalités spécifiques, ce qui sont ensuite exposée via un spécifique à la plateforme. Le résultat est un effet plus facilement consommable via XAML et un code fluent API.

Le processus de création spécifique à la plateforme est comme suit :

1. Implémenter des fonctionnalités spécifiques comme conséquence. Pour plus d’informations, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Créez une classe spécifique à la plateforme qui va exposer l’effet. Pour plus d’informations, consultez [création d’une classe spécifique à une plateforme](#creating-a-platform-specific-class).
1. Dans la classe spécifique à la plateforme, implémentez une propriété jointe pour autoriser le spécifiques à la plateforme à être consommée à partir de XAML. Pour plus d’informations, consultez [Ajout d’une propriété jointe](#adding-an-attached-property).
1. Dans la classe spécifique à la plateforme, implémentez les méthodes d’extension pour permettre la spécifiques à la plateforme à consommer via une API de code fluent. Pour plus d’informations, consultez [Ajout de méthodes d’extension](#adding-extension-methods).
1. Modifier l’implémentation de l’effet afin que l’effet est appliqué uniquement si la plateforme spécifique a été appelé sur la même plateforme que l’effet. Pour plus d’informations, consultez [création de l’effet](#creating-the-effect).

Le résultat d’exposer un effet que spécifiques à la plateforme est que l’effet peut être consommé plus facilement via XAML et une API de code fluent.

> [!NOTE]
> Il est prévu que les fournisseurs utilisera cette technique pour créer leurs propres caractéristiques de la plateforme, pour faciliter la consommation par les utilisateurs. Bien que les utilisateurs peuvent choisir de créer leurs propres caractéristiques de la plateforme, il convient de noter qu’il nécessite davantage de code que la création et utilisation d’un effet.

L' [exemple d’application](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) illustre un `Shadow` spécifique à la plateforme qui ajoute une ombre au texte affiché par un contrôle [`Label`](xref:Xamarin.Forms.Label) :

![](images/screenshots.png "Shadow Platform-Specific")

L' [exemple d’application](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implémente la `Shadow` spécifique à la plateforme sur chaque plateforme, pour faciliter la compréhension. Toutefois, à part de chaque implémentation d’effet spécifique à la plateforme, l’implémentation de la classe de clichés instantanés est en grande partie identique pour chaque plateforme. Par conséquent, ce guide est axé sur l’implémentation de la classe de clichés instantanés et l’effet associé sur une plateforme unique.

Pour plus d’informations sur les effets, consultez [Personnalisation des contrôles avec des effets](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Création d’une classe spécifique à la plateforme

Un spécifique à la plateforme est créé en tant que classe `public static` :

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Les sections suivantes décrivent l’implémentation de l’effet propre à la plateforme et à l' `Shadow`.

#### <a name="adding-an-attached-property"></a>Ajout d’une propriété jointe

Une propriété jointe doit être ajoutée au `Shadow` spécifique à la plateforme pour autoriser la consommation via XAML :

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        const string EffectName = "MyCompany.LabelShadowEffect";

        public static readonly BindableProperty IsShadowedProperty =
            BindableProperty.CreateAttached("IsShadowed",
                                            typeof(bool),
                                            typeof(Shadow),
                                            false,
                                            propertyChanged: OnIsShadowedPropertyChanged);

        public static bool GetIsShadowed(BindableObject element)
        {
            return (bool)element.GetValue(IsShadowedProperty);
        }

        public static void SetIsShadowed(BindableObject element, bool value)
        {
            element.SetValue(IsShadowedProperty, value);
        }

        ...

        static void OnIsShadowedPropertyChanged(BindableObject element, object oldValue, object newValue)
        {
            if ((bool)newValue)
            {
                AttachEffect(element as FormsElement);
            }
            else
            {
                DetachEffect(element as FormsElement);
            }
        }

        static void AttachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || controller.EffectIsAttached(EffectName))
            {
                return;
            }
            element.Effects.Add(Effect.Resolve(EffectName));
        }

        static void DetachEffect(FormsElement element)
        {
            IElementController controller = element;
            if (controller == null || !controller.EffectIsAttached(EffectName))
            {
                return;
            }

            var toRemove = element.Effects.FirstOrDefault(e => e.ResolveId == Effect.Resolve(EffectName).ResolveId);
            if (toRemove != null)
            {
                element.Effects.Remove(toRemove);
            }
        }
    }
}
```

La propriété jointe `IsShadowed` est utilisée pour ajouter le `MyCompany.LabelShadowEffect` effet à et le supprimer de, le contrôle auquel la classe `Shadow` est attachée. Cette propriété attachée inscrit la méthode `OnIsShadowedPropertyChanged` qui est exécutée au changement de la valeur de la propriété. Ensuite, cette méthode appelle la méthode `AttachEffect` ou `DetachEffect` pour ajouter ou supprimer l’effet en fonction de la valeur de la propriété jointe `IsShadowed`. L’effet est ajouté ou supprimé du contrôle en modifiant la collection de [`Effects`](xref:Xamarin.Forms.Element.Effects) du contrôle.

> [!NOTE]
> Notez que l’effet est résolu en spécifiant une valeur qui est une concaténation de la résolution de nom de groupe et un identificateur unique qui est spécifié sur l’implémentation de l’effet. Pour plus d’informations, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md).

Pour plus d’informations sur les propriétés jointes, consultez [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Ajout de méthodes d’Extension

Les méthodes d’extension doivent être ajoutées au `Shadow` spécifique à la plateforme pour permettre la consommation via une API de code fluent :

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
    using System.Linq;
    using Xamarin.Forms;
    using Xamarin.Forms.PlatformConfiguration;
    using FormsElement = Xamarin.Forms.Label;

    public static class Shadow
    {
        ...
        public static bool IsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config)
        {
            return GetIsShadowed(config.Element);
        }

        public static IPlatformElementConfiguration<iOS, FormsElement> SetIsShadowed(this IPlatformElementConfiguration<iOS, FormsElement> config, bool value)
        {
            SetIsShadowed(config.Element, value);
            return config;
        }
        ...
    }
}
```

Les méthodes d’extension `IsShadowed` et `SetIsShadowed` appellent les accesseurs obten et Set pour la propriété jointe `IsShadowed`, respectivement. Chaque méthode d’extension opère sur le type de `IPlatformElementConfiguration<iOS, FormsElement>`, qui spécifie que le spécifique à la plateforme peut être appelé sur des instances de [`Label`](xref:Xamarin.Forms.Label) à partir d’iOS.

#### <a name="creating-the-effect"></a>Création de l’effet

Le `Shadow` spécifique à la plateforme ajoute le `MyCompany.LabelShadowEffect` à un [`Label`](xref:Xamarin.Forms.Label), puis le supprime. L’exemple de code suivant illustre l’implémentation de `LabelShadowEffect` pour le projet iOS :

```csharp
[assembly: ResolutionGroupName("MyCompany")]
[assembly: ExportEffect(typeof(LabelShadowEffect), "LabelShadowEffect")]
namespace ShadowPlatformSpecific.iOS
{
    public class LabelShadowEffect : PlatformEffect
    {
        protected override void OnAttached()
        {
            UpdateShadow();
        }

        protected override void OnDetached()
        {
        }

        protected override void OnElementPropertyChanged(PropertyChangedEventArgs args)
        {
            base.OnElementPropertyChanged(args);

            if (args.PropertyName == Shadow.IsShadowedProperty.PropertyName)
            {
                UpdateShadow();
            }
        }

        void UpdateShadow()
        {
            try
            {
                if (((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.CornerRadius = 5;
                    Control.Layer.ShadowColor = UIColor.Black.CGColor;
                    Control.Layer.ShadowOffset = new CGSize(5, 5);
                    Control.Layer.ShadowOpacity = 1.0f;
                }
                else if (!((Label)Element).OnThisPlatform().IsShadowed())
                {
                    Control.Layer.ShadowOpacity = 0;
                }
            }
            catch (Exception ex)
            {
                Console.WriteLine("Cannot set property on attached control. Error: ", ex.Message);
            }
        }
    }
}
```

La méthode `UpdateShadow` définit `Control.Layer` propriétés pour créer l’ombre, à condition que la propriété jointe `IsShadowed` ait la valeur `true`, et à condition que le `Shadow` spécifique à la plateforme ait été appelé sur la plateforme pour laquelle l’effet est implémenté. Cette vérification s’effectue à l’aide de la méthode `OnThisPlatform`.

Si la `Shadow.IsShadowed` valeur de la propriété jointe change au moment de l’exécution, l’effet doit répondre en supprimant l’ombre. Par conséquent, une version substituée de la méthode `OnElementPropertyChanged` est utilisée pour répondre à la modification de la propriété pouvant être liée en appelant la méthode `UpdateShadow`.

Pour plus d’informations sur la création d’un effet, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md) et [passage de paramètres d’effet en tant que propriétés jointes](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Utilisation du spécifique à la plateforme

Le `Shadow` spécifique à la plateforme est consommé en XAML en affectant à la propriété jointe `Shadow.IsShadowed` la valeur `boolean` :

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Vous pouvez également, il peut être consommé à partir de c# à l’aide de l’API fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Caractéristiques de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Caractéristiques de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)
- [Personnalisation des contrôles avec des effets](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)
