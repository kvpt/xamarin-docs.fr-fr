---
title: Clés d’accès VisualElement sur Windows
description: Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés. Cet article explique comment utiliser le spécifique à la plate-forme Windows qui spécifie une clé d’accès pour un VisualElement.
ms.prod: xamarin
ms.assetid: 771AF785-76B8-4372-89F5-E4D521D21E0C
ms.technology: xamarin-forms
author: davidbritch
ms.author: dabritch
ms.date: 10/24/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 7df914ae5b6de38b5179f5b1139b9c41e9559198
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93367450"
---
# <a name="visualelement-access-keys-on-windows"></a>Clés d’accès VisualElement sur Windows

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)

Les touches d’accès rapide sont des raccourcis clavier qui améliorent la convivialité et l’accessibilité des applications sur le plateforme Windows universelle (UWP) en fournissant un moyen intuitif aux utilisateurs de naviguer rapidement et d’interagir avec l’interface utilisateur visible de l’application par le biais d’un clavier plutôt que par une touche tactile ou une souris. Il s’agit de combinaisons de la touche Alt et d’une ou de plusieurs touches alphanumériques, généralement appuyées de manière séquentielle. Les raccourcis clavier sont automatiquement pris en charge pour les touches d’accès en utilisant un seul caractère alphanumérique.

Les touches d’accès sont des badges flottants affichés à côté des contrôles qui incluent des clés d’accès. Chaque info-bulle de clé d’accès contient les touches alphanumériques qui activent le contrôle associé. Quand un utilisateur appuie sur la touche Alt, les conseils sur les touches d’accès sont affichés.

Ce propre à la plateforme UWP est utilisé pour spécifier une clé d’accès pour un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Il est consommé en XAML en affectant [`VisualElement.AccessKey`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyProperty) à la propriété jointe une valeur alphanumérique et en affectant éventuellement à la [`VisualElement.AccessKeyPlacement`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyPlacementProperty) propriété jointe une valeur de l' [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) énumération, [`VisualElement.AccessKeyHorizontalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyHorizontalOffsetProperty) à la propriété jointe un `double` et à la [`VisualElement.AccessKeyVerticalOffset`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific.VisualElement.AccessKeyVerticalOffsetProperty) propriété jointe `double` :

```xaml
<TabbedPage ...
            xmlns:windows="clr-namespace:Xamarin.Forms.PlatformConfiguration.WindowsSpecific;assembly=Xamarin.Forms.Core">
    <ContentPage Title="Page 1"
                 windows:VisualElement.AccessKey="1">
        <StackLayout Margin="20">
            ...
            <Switch windows:VisualElement.AccessKey="A" />
            <Entry Placeholder="Enter text here"
                   windows:VisualElement.AccessKey="B" />
            ...
            <Button Text="Access key F, placement top with offsets"
                    Margin="20"
                    Clicked="OnButtonClicked"
                    windows:VisualElement.AccessKey="F"
                    windows:VisualElement.AccessKeyPlacement="Top"
                    windows:VisualElement.AccessKeyHorizontalOffset="20"
                    windows:VisualElement.AccessKeyVerticalOffset="20" />
            ...
        </StackLayout>
    </ContentPage>
    ...
</TabbedPage>
```

Elle peut également être utilisée à partir de C# à l’aide de l’API Fluent :

```csharp
using Xamarin.Forms.PlatformConfiguration;
using Xamarin.Forms.PlatformConfiguration.WindowsSpecific;
...

var page = new ContentPage { Title = "Page 1" };
page.On<Windows>().SetAccessKey("1");

var switchView = new Switch();
switchView.On<Windows>().SetAccessKey("A");
var entry = new Entry { Placeholder = "Enter text here" };
entry.On<Windows>().SetAccessKey("B");
...

var button4 = new Button { Text = "Access key F, placement top with offsets", Margin = new Thickness(20) };
button4.Clicked += OnButtonClicked;
button4.On<Windows>()
    .SetAccessKey("F")
    .SetAccessKeyPlacement(AccessKeyPlacement.Top)
    .SetAccessKeyHorizontalOffset(20)
    .SetAccessKeyVerticalOffset(20);
...
```

La `VisualElement.On<Windows>` méthode spécifie que ce spécifique à la plateforme s’exécutera uniquement sur le plateforme Windows universelle. [ `VisualElement.SetAccessKey` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, System. String)), dans l' [`Xamarin.Forms.PlatformConfiguration.WindowsSpecific`](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific) espace de noms, est utilisé pour définir la valeur de clé d’accès pour le `VisualElement` . [ `VisualElement.SetAccessKeyPlacement` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, Xamarin.Forms . AccessKeyPlacement)), spécifie éventuellement la position à utiliser pour afficher l’info-bulle de clé d’accès, avec l' [`AccessKeyPlacement`](xref:Xamarin.Forms.AccessKeyPlacement) énumération qui fournit les valeurs possibles suivantes :

- [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) : indique que le placement de l’info-bulle de clé d’accès sera déterminé par le système d’exploitation.
- [`Top`](xref:Xamarin.Forms.AccessKeyPlacement.Top) : indique que l’info-bulle de la touche d’accès est affichée au-dessus du bord supérieur du `VisualElement` .
- [`Bottom`](xref:Xamarin.Forms.AccessKeyPlacement.Bottom) : indique que l’info-bulle de la touche d’accès apparaît sous le bord inférieur du `VisualElement` .
- [`Right`](xref:Xamarin.Forms.AccessKeyPlacement.Right) : indique que l’info-bulle de la touche d’accès s’affiche à droite du bord droit du `VisualElement` .
- [`Left`](xref:Xamarin.Forms.AccessKeyPlacement.Left) : indique que l’info-bulle de la touche d’accès s’affiche à gauche du bord gauche du `VisualElement` .
- [`Center`](xref:Xamarin.Forms.AccessKeyPlacement.Center) : indique que l’info-bulle de la touche d’accès s’affiche sur le centre du `VisualElement` .

> [!NOTE]
> En règle générale, le [`Auto`](xref:Xamarin.Forms.AccessKeyPlacement.Auto) positionnement de l’embout clé est suffisant, ce qui comprend la prise en charge des interfaces utilisateur adaptatives.

[ `VisualElement.SetAccessKeyHorizontalOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}, System. double)) et [ `VisualElement.SetAccessKeyVerticalOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. SetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . Les méthodes VisualElement}, System. double)) peuvent être utilisées pour un contrôle plus granulaire de l’emplacement de l’info-bulle de clé d’accès. L’argument de la `SetAccessKeyHorizontalOffset` méthode indique le déplacement de l’info-bulle vers la gauche ou la droite de la touche d’accès et l’argument de la `SetAccessKeyVerticalOffset` méthode indique la distance de déplacement de l’info-bulle de la touche d’accès.

>[!NOTE]
> Les décalages d’info-bulle de clé d’accès ne peuvent pas être définis lorsque l’emplacement de la clé d’accès est défini `Auto` .

En outre, le [ `GetAccessKey` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKey ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyPlacement` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyPlacement ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})), [ `GetAccessKeyHorizontalOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyHorizontalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement})) et [ `GetAccessKeyVerticalOffset` ] (XREF : Xamarin.Forms . PlatformConfiguration. WindowsSpecific. VisualElement. GetAccessKeyVerticalOffset ( Xamarin.Forms . IPlatformElementConfiguration { Xamarin.Forms . PlatformConfiguration. Windows, Xamarin.Forms . VisualElement}))) peuvent être utilisées pour récupérer une valeur de clé d’accès et son emplacement.

Le résultat est que les conseils de clé d’accès peuvent être affichés en regard de toutes les [`VisualElement`](xref:Xamarin.Forms.VisualElement) instances qui définissent les touches d’accès, en appuyant sur la touche Alt :

![Clés d’accès VisualElement spécifiques à la plateforme](visualelement-access-keys-images/visualelement-accesskeys.png "Clés d’accès VisualElement spécifiques à la plateforme")

Lorsqu’un utilisateur active une touche d’accès, en appuyant sur la touche Alt suivie de la touche d’accès, l’action par défaut pour le `VisualElement` est exécutée. Par exemple, lorsqu’un utilisateur active la touche d’accès sur un [`Switch`](xref:Xamarin.Forms.Switch) , `Switch` est basculé. Lorsqu’un utilisateur active la touche d’accès sur un [`Entry`](xref:Xamarin.Forms.Entry) , le a le `Entry` focus. Lorsqu’un utilisateur active la touche d’accès sur un [`Button`](xref:Xamarin.Forms.Button) , le gestionnaire d’événements pour l' [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement est exécuté.

Pour plus d’informations sur les clés d’accès, consultez [clés d’accès](/windows/uwp/design/input/access-keys#key-tip-positioning).

## <a name="related-links"></a>Liens connexes

- [PlatformSpecifics (exemple)](/samples/xamarin/xamarin-forms-samples/userinterface-platformspecifics)
- [Création de caractéristiques de la plateforme](~/xamarin-forms/platform/platform-specifics/index.md#creating-platform-specifics)
- [API WindowsSpecific](xref:Xamarin.Forms.PlatformConfiguration.WindowsSpecific)