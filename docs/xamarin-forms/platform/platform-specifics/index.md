---
title: Spécificités de la plateforme
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser et créer des spécifiques à la plateforme.
ms.prod: xamarin
ms.assetid: 4729DB9C-8800-4E29-9D66-3BE13C5F8C94
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/01/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: b029ffdb0a16d9f54951ae3754ca0f1eb7f69aaa
ms.sourcegitcommit: 122b8ba3dcf4bc59368a16c44e71846b11c136c5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/30/2020
ms.locfileid: "91556878"
---
# <a name="platform-specifics"></a>Spécificités de la plateforme

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

_Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés._

Le processus permettant de consommer un spécifique à une plateforme par le biais de XAML, ou via l’API de code fluent, est le suivant :

1. Ajoutez une `xmlns` déclaration ou une `using` directive pour l' [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) espace de noms.
1. Ajoutez une `xmlns` déclaration ou une `using` directive pour l’espace de noms qui contient les fonctionnalités spécifiques à la plateforme :
    1. Sur iOS, il s’agit de l' [`Xamarin.Forms.PlatformConfiguration.iOSSpecific`](xref:Xamarin.Forms.PlatformConfiguration.iOSSpecific) espace de noms.
    1. Sur Android, il s’agit de l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific) espace de noms. Pour Android AppCompat, il s’agit de l' [`Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat`](xref:Xamarin.Forms.PlatformConfiguration.AndroidSpecific.AppCompat) espace de noms.
    1. Sur la plateforme Windows universelle, il s’agit de l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms.
1. Appliquez la spécifique à la plateforme à partir de XAML, ou à partir du code avec l' `On<T>` API Fluent. La valeur de `T` peut être les [`iOS`](xref:Xamarin.Forms.PlatformConfiguration.iOS) [`Android`](xref:Xamarin.Forms.PlatformConfiguration.Android) types, ou [`Windows`](xref:Xamarin.Forms.PlatformConfiguration.Windows) de l' [`Xamarin.Forms.PlatformConfiguration`](xref:Xamarin.Forms.PlatformConfiguration) espace de noms.

> [!NOTE]
> Notez que toute tentative d’utilisation d’un spécifique à une plateforme sur une plateforme où elle n’est pas disponible ne génère pas d’erreur. Au lieu de cela, le code s’exécute sans que le spécifique à la plateforme soit appliqué.

Les caractéristiques de la plateforme consommées via l' `On<T>` API de code fluent retournent des [`IPlatformElementConfiguration`](xref:Xamarin.Forms.IPlatformElementConfiguration`2) objets. Cela permet à plusieurs spécificités de la plateforme d’être appelées sur le même objet avec la méthode en cascade.

Pour plus d’informations sur les spécificités de la plateforme fournies par, consultez caractéristiques spécifiques à la plateforme Xamarin.Forms [iOS](~/xamarin-forms/platform/ios/index.md), caractéristiques spécifiques à la plateforme [Android](~/xamarin-forms/platform/android/index.md)et [caractéristiques propres à la plateforme Windows](~/xamarin-forms/platform/windows/index.md).

## <a name="creating-platform-specifics"></a>Création de caractéristiques spécifiques à la plateforme

Les fournisseurs peuvent créer leurs propres propres plateformes avec des effets. Un effet fournit les fonctionnalités spécifiques, qui sont ensuite exposées via un spécifique à une plateforme. Le résultat est un effet qui peut être plus facilement consommé par le biais du XAML et par le biais d’une API de code fluent.

Le processus de création d’un spécifique à une plateforme est le suivant :

1. Implémentez les fonctionnalités spécifiques comme un effet. Pour plus d’informations, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md).
1. Créez une classe spécifique à la plateforme qui expose l’effet. Pour plus d’informations, consultez [création d’une classe spécifique à une plateforme](#creating-a-platform-specific-class).
1. Dans la classe spécifique à la plateforme, implémentez une propriété jointe pour permettre l’utilisation de la plateforme spécifique via XAML. Pour plus d’informations, consultez [Ajout d’une propriété jointe](#adding-an-attached-property).
1. Dans la classe spécifique à la plateforme, implémentez des méthodes d’extension pour permettre l’utilisation de la plateforme spécifique via une API de code fluent. Pour plus d’informations, consultez [Ajout de méthodes d’extension](#adding-extension-methods).
1. Modifiez l’implémentation de l’effet afin que l’effet soit appliqué uniquement si le spécifique à la plateforme a été appelé sur la même plateforme que l’effet. Pour plus d’informations, consultez [création de l’effet](#creating-the-effect).

Le résultat de l’exposition d’un effet en tant que spécifique à une plateforme est que l’effet peut être plus facilement consommé par le biais du XAML et par le biais d’une API de code fluent.

> [!NOTE]
> Il est prévu que les fournisseurs utilisent cette technique pour créer leurs propres propres plateformes, afin de faciliter la consommation par les utilisateurs. Bien que les utilisateurs puissent choisir de créer leurs propres propres plateformes, il convient de noter qu’il nécessite davantage de code que la création et l’utilisation d’un effet.

L' [exemple d’application](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) illustre un `Shadow` spécifique à une plateforme qui ajoute une ombre au texte affiché par un [`Label`](xref:Xamarin.Forms.Label) contrôle :

![Shadow-spécifique à la plateforme](images/screenshots.png)

L' [exemple d’application](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific) implémente la `Shadow` plateforme spécifique à chaque plateforme, afin de faciliter la compréhension. Toutefois, hormis chaque implémentation d’effet propre à la plateforme, l’implémentation de la classe Shadow est largement identique pour chaque plateforme. Par conséquent, ce guide se concentre sur l’implémentation de la classe Shadow et l’effet associé sur une plate-forme unique.

Pour plus d’informations sur les effets, consultez [Personnalisation des contrôles avec des effets](~/xamarin-forms/app-fundamentals/effects/index.md).

### <a name="creating-a-platform-specific-class"></a>Création d’une classe spécifique à la plateforme

Un spécifique à la plateforme est créé en tant que `public static` classe :

```csharp
namespace MyCompany.Forms.PlatformConfiguration.iOS
{
  public static Shadow
  {
    ...
  }
}
```

Les sections suivantes décrivent l’implémentation de l' `Shadow` effet propre à la plateforme et associé.

#### <a name="adding-an-attached-property"></a>Ajout d’une propriété jointe

Une propriété jointe doit être ajoutée à la `Shadow` plateforme spécifique pour autoriser la consommation via XAML :

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

La `IsShadowed` propriété jointe est utilisée pour ajouter l' `MyCompany.LabelShadowEffect` effet à et le supprimer de, le contrôle auquel la `Shadow` classe est attachée. Cette propriété jointe inscrit la méthode `OnIsShadowedPropertyChanged` qui est exécutée au changement de la valeur de la propriété. Ensuite, cette méthode appelle la `AttachEffect` méthode ou `DetachEffect` pour ajouter ou supprimer l’effet en fonction de la valeur de la `IsShadowed` propriété jointe. L’effet est ajouté ou supprimé du contrôle en modifiant la collection du contrôle [`Effects`](xref:Xamarin.Forms.Element.Effects) .

> [!NOTE]
> Notez que l’effet est résolu en spécifiant une valeur qui est une concaténation du nom du groupe de résolutions et de l’identificateur unique spécifié sur l’implémentation de l’effet. Pour plus d’informations, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md).

Pour plus d’informations sur les propriétés jointes, consultez [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md).

#### <a name="adding-extension-methods"></a>Ajout de méthodes d’extension

Les méthodes d’extension doivent être ajoutées à la `Shadow` plateforme spécifique pour permettre la consommation via une API de code fluent :

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

Les `IsShadowed` `SetIsShadowed` méthodes d’extension et appellent les accesseurs obten et Set pour la `IsShadowed` propriété jointe, respectivement. Chaque méthode d’extension opère sur le `IPlatformElementConfiguration<iOS, FormsElement>` type, qui spécifie que le spécifique à la plateforme peut être appelé sur des [`Label`](xref:Xamarin.Forms.Label) instances d’iOS.

#### <a name="creating-the-effect"></a>Création de l’effet

Le `Shadow` spécifique à la plateforme ajoute le `MyCompany.LabelShadowEffect` à un [`Label`](xref:Xamarin.Forms.Label) , puis le supprime. L’exemple de code suivant illustre l’implémentation de `LabelShadowEffect` pour le projet iOS :

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

La `UpdateShadow` méthode définit `Control.Layer` les propriétés pour créer l’ombre, à condition que la `IsShadowed` propriété jointe ait la valeur `true` , et à condition que le spécifique à la `Shadow` plateforme ait été appelé sur la plateforme pour laquelle l’effet est implémenté. Cette vérification s’effectue à l’aide de la `OnThisPlatform` méthode.

Si la `Shadow.IsShadowed` valeur de la propriété jointe change au moment de l’exécution, l’effet doit répondre en supprimant l’ombre. Par conséquent, une version substituée de la `OnElementPropertyChanged` méthode est utilisée pour répondre à la modification de la propriété pouvant être liée en appelant la `UpdateShadow` méthode.

Pour plus d’informations sur la création d’un effet, consultez [création d’un effet](~/xamarin-forms/app-fundamentals/effects/creating.md) et [passage de paramètres d’effet en tant que propriétés jointes](~/xamarin-forms/app-fundamentals/effects/passing-parameters/attached-properties.md).

### <a name="consuming-the-platform-specific"></a>Utilisation du spécifique à la plateforme

Le `Shadow` spécifique à la plateforme est consommé en XAML en affectant `Shadow.IsShadowed` une valeur à la propriété jointe `boolean` :

```xaml
<ContentPage xmlns:ios="clr-namespace:MyCompany.Forms.PlatformConfiguration.iOS" ...>
  ...
  <Label Text="Label Shadow Effect" ios:Shadow.IsShadowed="true" ... />
  ...
</ContentPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using MyCompany.Forms.PlatformConfiguration.iOS;

...

shadowLabel.On<iOS>().SetIsShadowed(true);
```

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [ShadowPlatformSpecific (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-shadowplatformspecific)
- [Spécificités de la plateforme iOS](~/xamarin-forms/platform/ios/index.md)
- [Spécificités de la plateforme Android](~/xamarin-forms/platform/android/index.md)
- [Spécificités de la plateforme Windows](~/xamarin-forms/platform/windows/index.md)
- [Personnalisation des contrôles avec des effets](~/xamarin-forms/app-fundamentals/effects/index.md)
- [Propriétés jointes](~/xamarin-forms/xaml/attached-properties.md)
- [API PlatformConfiguration](xref:Xamarin.Forms.PlatformConfiguration)