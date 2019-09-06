---
ms.assetid: 1BB412D1-FC3D-4E69-8B01-B976A3DB6328
title: 'WPF et Xamarin. Forms : Similarités & différences'
description: Ce document compare et contraste WPF avec Xamarin. Forms. Il traite des modèles de contrôle, du XAML, de l’infrastructure de liaison, des modèles de données, d’ItemsControl, de UserControl, de la navigation et de la navigation dans les URL.
author: conceptdev
ms.author: crdun
ms.date: 04/26/2017
ms.openlocfilehash: d23b449382183b0385eac38c0b9205e48dbe0a34
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70290406"
---
# <a name="wpf-vs-xamarinforms-similarities--differences"></a>WPF et Xamarin. Forms : Similarités & différences

## <a name="control-templates"></a>Modèles de contrôle

WPF prend en charge le concept de *modèles de contrôle* qui fournissent les instructions de visualisation`Button`pour `ListBox`un contrôle (,, etc.). Comme indiqué ci-dessus, Xamarin. Forms utilise des classes de _rendu_ concrètes pour ce qui interagissent avec la plateforme native (iOS, Android, etc.) pour visualiser le contrôle.

Toutefois, Xamarin _. Forms a_ un `ControlTemplate` type, il est `Page` utilisé pour les objets. Il fournit une définition pour un `Page` qui fournit du contenu cohérent, mais permet à l’utilisateur de la page de modifier les couleurs, les polices, etc., et même d’ajouter des éléments pour le rendre unique à l’application.

Les utilisations courantes de ce sont des tâches telles que les boîtes de dialogue d’authentification, les invites et la fourniture d’un aspect de page standardisé, mais pouvant être personnalisé, qui peut être personnalisé au sein de l’application. Dans le cadre de cette prise en charge, de nombreux contrôles familiers nommés WPF sont utilisés :

1. `ContentPage`
2. `ContentView`
3. `ContentPresenter`
4. `TemplateBinding`

Mais il est important de savoir qu’elles _ne servent pas_ le même objectif dans Xamarin. Forms. Pour plus d’informations sur cette fonctionnalité, consultez la [page de documentation](~/xamarin-forms/app-fundamentals/templates/control-templates/index.md).

## <a name="xaml"></a>XAML

XAML est utilisé comme langage de balisage déclaratif pour WPF et Xamarin. Forms. Pour l’essentiel, la syntaxe est identique : la principale différence est celle des objets définis/créés par les graphiques XAML.

- Xamarin. Forms prend en charge la [spécification XAML 2009](/dotnet/framework/xaml-services/xaml-2009-language-features/); Il est ainsi plus facile de définir des données `string`telles que `int`des s, des, etc., ainsi que de définir des types génériques et de passer des arguments à des constructeurs.

- Il n’existe actuellement aucun moyen de charger dynamiquement du code XAML, `XamlReader`comme WPF peut le faire avec. Vous pouvez cependant obtenir les mêmes fonctionnalités de base avec un [package NuGet](https://www.nuget.org/packages/Xamarin.Forms.Dynamic/) .

### <a name="markup-extensions"></a>Extensions de balisage

Xamarin. Forms prend en charge l’extension du code XAML par le biais des extensions de balisage, comme WPF. Les mêmes blocs de construction de base sont les mêmes :

1. `{x:Array}`
2. `{Binding}`
3. `{DynamicResource}`
4. `{x:Null}`
5. `{x:Static}`
6. `{StaticResource}`
7. `{x:Type}`

En outre, il comprend `{x:Reference}` de la spécification XAML 2009 et une `{TemplateBinding}` extension de balisage qui est utilisée pour la version spécialisée `ControlTemplate` de prise en charge par Xamarin. Forms.

> [!WARNING]
> La `ControlTemplate` prise en charge n’est pas la même, même si elle porte le même nom.

Xamarin. Forms prend également en charge les extensions de balisage personnalisées, mais l’implémentation est légèrement différente. Dans WPF, vous devez dériver `MarkupExtension` à partir d’une classe de base abstraite. Dans Xamarin. Forms, qui est remplacé par une `IMarkupExtension` interface `IMarkupExtension<T>` ou qui est plus flexible.

Tout comme WPF, la seule méthode requise est une `ProvideValue` méthode permettant de retourner la valeur à partir de l’extension de balisage.

## <a name="binding-infrastructure"></a>Infrastructure de liaison

L’un des concepts de base reportés est une infrastructure de liaison de données permettant de connecter des propriétés visuelles à des propriétés de données .NET. Cela permet d’activer des modèles architecturaux tels que MVVM. La conception de base est identique : vous avez une classe de base pouvant être liée [bindableobject](xref:Xamarin.Forms.BindableObject), dans WPF, il s’agit de la classe [DependencyObject](xref:System.Windows.DependencyObject) . Cette classe de base est utilisée comme ancêtre racine pour tous les objets qui feront partie de cibles dans la liaison de données. Les classes dérivées exposent ensuite les objets [BindableProperty](xref:Xamarin.Forms.BindableProperty) qui jouent le rôle de stockage de sauvegarde pour les valeurs de propriété (ils sont définis en tant qu’objets [DependencyProperty](xref:System.Windows.DependencyProperty) dans WPF).

### <a name="defining-bindable-properties"></a>Définition des propriétés pouvant être liées

La définition d’une propriété pouvant être liée dans Xamarin. Forms est la même que WPF :
1. L’objet doit dériver `BindableObject`de.
2. Un champ statique public de type `BindableProperty` doit être déclaré pour définir la clé de stockage de stockage pour la propriété.
3. Il doit y avoir un wrapper de propriété d’instance `GetValue` publique `SetValue` qui utilise et pour récupérer et modifier la valeur des propriétés.

Pour obtenir un exemple complet, consultez [propriétés pouvant être liées dans Xamarin. Forms](~/xamarin-forms/xaml/bindable-properties.md).

### <a name="attached-properties"></a>Propriétés jointes

Les propriétés jointes sont un sous-ensemble de la propriété pouvant être liée et fonctionnent de la même façon que dans WPF. La principale différence réside dans le fait que le wrapper de propriété est ommitted dans ce cas et remplacé par un ensemble de méthodes d’extraction/définition statiques sur la classe propriétaire. Pour plus d’informations, consultez [propriétés jointes dans Xamarin. Forms](~/xamarin-forms/xaml/attached-properties.md) .

### <a name="using-the-binding-engine"></a>Utilisation du moteur de liaison

Le processus d’utilisation du moteur de liaison est le même que dans WPF. Il peut être utilisé dans le code-behind en créant un `Binding` objet lié à un objet source (tout type .net) et une valeur de propriété facultative (si ommitted, il traite l’objet source comme la propriété elle-même, comme WPF). Vous pouvez ensuite utiliser `SetBinding` sur n' `BindableObject` importe lequel pour associer la liaison `BindableProperty`à un.

Vous pouvez également définir la relation de liaison en XAML à l’aide `BindingExtension`de. Il a les mêmes valeurs de base que l’extension dans WPF.

La prise en charge de la liaison et le moteur sont plus similaires à l’implémentation de Silverlight que WPF. Il existe plusieurs fonctionnalités manquantes qui n’ont pas été implémentées dans Xamarin. Forms :

- Il n’existe aucune prise en charge pour les fonctionnalités suivantes dans les liaisons :
  - BindingGroupName
  - BindsDirectlyToSource
  - IsAsync
  - MultiBinding
  - NotifyOnSourceUpdated
  - NotifyOnTargetUpdated
  - NotifyOnValidationError
  - UpdateSourceTrigger
  - UpdateSourceExceptionFilter
  - ValidatesOnDataErrors
  - ValidatesOnExceptions
  - Collection ValidationRules
  - XPath
  - XmlNamespaceManager

#### <a name="relativesource"></a>RelativeSource

Il n’existe aucune prise `RelativeSource` en charge des liaisons. Dans WPF, elles vous permettent d’effectuer une liaison à d’autres éléments visuels définis en XAML. Dans Xamarin. Forms, cette même fonctionnalité peut être obtenue à `{x:Reference}` l’aide de l’extension de balisage. Par exemple, en supposant que nous avons un contrôle portant le nom « otherControl » qui a une propriété de texte, nous pouvons y créer une liaison de ce type :

**WPF**

```xaml
Text={Binding RelativeSource={RelativeSource otherControl}, Path=Text}
```

**Xamarin.Forms**

```xaml
Text={Binding Source={x:Reference otherControl}, Path=Text}
```

La même fonctionnalité peut être utilisée pour la `{RelativeSource Self}` fonctionnalité. Toutefois, il n’existe pas de prise en charge pour localiser`{RelativeSource FindAncestor}`les ancêtres par type ().

#### <a name="binding-context"></a>Contexte de liaison

Dans WPF, vous pouvez définir une `DataContext` valeur de propriété qui reprents la source de liaison par défaut. Si la source d’une liaison n’est pas définie, cette valeur de propriété est utilisée. La valeur est héritée de l’arborescence d’éléments visuels, ce qui lui permet d’être définie à un niveau supérieur, puis utilisée par les enfants.

Dans Xamarin. Forms, cette même fonctionnalité est disponibles, mais le nom de `BindingContext`la propriété est.

#### <a name="value-converters"></a>convertisseurs de valeurs ;

Les convertisseurs de valeurs sont entièrement pris en charge dans Xamarin. Forms, tout comme WPF. La même forme interface est utilisée, mais Xamarin. Forms a l’interface définie dans `Xamarin.Forms` l’espace de noms.

### <a name="model-view-viewmodel"></a>Model-View-ViewModel

MVVM est entièrement pris en charge par WPF et Xamarin. Forms.

WPF comprend un intégré `RoutedCommand` qui est parfois utilisé ; Xamarin. Forms n’a pas de prise en charge des commandes `ICommand` intégrées au-delà de la définition de l’interface. Vous pouvez inclure diverses infrastructures MVVM pour ajouter les classes de base nécessaires pour implémenter MVVM.

#### <a name="inotifypropertychanged-and-inotifycollectionchanged"></a>INotifyPropertyChanged et INotifyCollectionChanged

Les deux interfaces sont entièrement prises en charge dans les liaisons Xamarin. Forms. Contrairement à de nombreuses infrastructures basées sur XAML, les notifications de modification de propriété peuvent être déclenchées sur les threads d’arrière-plan dans Xamarin. Forms (tout comme WPF) et le moteur de liaison passera correctement au thread d’interface utilisateur.

En outre, les deux environnements `SynchronziationContext` prennent `async` en charge et / `await` effectuent le marshaling de thread approprié. WPF inclut la `Dispatcher` classe sur tous les éléments visuels, Xamarin. Forms a une `Device.BeginInvokeOnMainThread` méthode statique qui peut également être utilisée `SynchronizationContext` (même si est préféré pour le codage multiplateforme).

- Xamarin. Forms comprend `ObservableCollection<T>` un qui prend en charge les notifications de modification de collection.
- Vous pouvez utiliser `BindingBase.EnableCollectionSynchronization` pour activer les mises à jour inter-threads pour une collection. L’API est légèrement différente de la variante WPF. [pour plus d’informations sur l’utilisation, consultez la documentation](xref:Xamarin.Forms.BindingBase.EnableCollectionSynchronization*).

## <a name="data-templates"></a>Modèles de données

Les modèles de données sont pris en charge dans Xamarin. Forms pour `ListView` personnaliser le rendu d’une ligne (cellule). Contrairement à WPF qui peut `DataTemplate`utiliser des s pour tout contrôle orienté contenu, Xamarin. Forms les utilise actuellement `ListView`uniquement pour. La définition de modèle peut être définie en ligne (affectée à `ItemTemplate` la propriété) ou en tant que ressource dans `ResourceDictionary`un.

En outre, ils ne sont pas aussi flexibles que leurs équivalents WPF.

1. L’élément racine de `DataTemplate` doit _toujours_ être un `ViewCell` objet.
2. Les déclencheurs de données sont entièrement pris en charge dans un modèle de données `DataType` , mais doivent inclure une propriété indiquant le type de la propriété à laquelle le déclencheur est associé.
3. `DataTemplateSelector`est également pris en charge, mais dérive de `DataTemplate` et, par conséquent, vient juste d’être assigné directement à la `ItemTemplate` propriété ( `ItemTemplateSelector` par rapport à WPF).

## <a name="itemscontrol"></a>ItemsControl

Il n’existe aucun équivalent intégré à un `ItemsControl` dans Xamarin. Forms ; mais il existe un [personnalisé pour Xamarin. Forms disponible ici](https://github.com/xamarinhq/xamu-infrastructure/blob/master/src/XamU.Infrastructure/Controls/ItemsControl.cs).

## <a name="user-controls"></a>Contrôles utilisateur

Dans WPF, `UserControl`les s sont utilisées pour fournir une section de l’interface utilisateur associée au comportement. Dans Xamarin. Forms, nous utilisons `ContentView` le dans le même but. Les deux prennent en charge la liaison et l’inclusion en XAML.

## <a name="navigation"></a>Navigation

WPF comprend un rarement utilisé `NavigationService` qui peut être utilisé pour fournir une fonctionnalité de navigation « de type navigateur ». La plupart des applications ne se sont pas dérangées `Window` , mais utilisaient des éléments différents ou des sections différentes de la fenêtre pour afficher les données.

Sur les appareils téléphoniques, les différents _écrans_ sont souvent la solution et donc Xamarin. Forms prend en charge plusieurs formes de navigation :

| Style de navigation | Type de page |
|--- |--- |
|Basée sur la pile (push/pop)|NavigationPage|
|Maître/détail|MasterDetailPage|
|Tabulations|TabbedPage|
|Balayer vers la gauche/droite|CarouselView|

Est l’approche la plus courante, et chaque page a une `Navigation` propriété qui peut être utilisée pour pousser ou dépiler des pages sur la pile de navigation. `NavigationPage` Il s’agit de l’équivalent le plus `NavigationService` proche du trouvé dans WPF.

### <a name="url-navigation"></a>Navigation dans les URL

WPF est une technologie orientée Bureau qui peut accepter des paramètres de ligne de commande pour diriger le comportement de démarrage. Xamarin. Forms peut utiliser la [liaison d’URL profonde](https://blog.xamarin.com/deep-link-content-with-xamarin-forms-url-navigation/) pour accéder à une page au démarrage.
