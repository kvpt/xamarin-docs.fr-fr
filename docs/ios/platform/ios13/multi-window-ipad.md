---
title: Plusieurs fenêtres pour iPad dans Xamarin. iOS
description: Ajoutez une prise en charge de plusieurs fenêtres à vos applications iPad.
ms.prod: xamarin
ms.assetid: 524b6f2e-dbdf-11e9-8a34-2a2ae2dbcce4
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 09/20/2019
ms.openlocfilehash: b3f96f342679d8be6d2f8fbc0ad5962ca675d2a5
ms.sourcegitcommit: 09bc69d7119a04684c9e804c5cb113b8b1bb7dfc
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/24/2019
ms.locfileid: "71213792"
---
# <a name="multiple-windows-for-ipad"></a>Plusieurs fenêtres pour iPad

iOS 13 prend désormais en charge les fenêtres côte à côte pour la même application sur iPad. Cela permet de nouvelles expériences et des interactions de glisser-déplacer entre les fenêtres. Ce document vous montre comment configurer votre application pour prendre en charge cette fonctionnalité et introduit ces nouvelles fonctionnalités. 

## <a name="project-configuration"></a>Configuration du projet

Pour configurer votre projet pour plusieurs fenêtres, modifiez le `info.plist` pour déclarer `NSUserActivityTypes` à IOS votre application gèrera les activités de ce type, `UIApplicationSceneManifest` et pour `UIApplicationSupportsMultipleScenes` activer plusieurs fenêtres et `UISceneConfigurations` associer votre scène avec un Storyboard.

```xml
<key>NSUserActivityTypes</key>
<array>
    <string>com.xamarin.Gallery.openDetail</string>
</array>
<key>UIApplicationSceneManifest</key>
<dict>
    <key>UIApplicationSupportsMultipleScenes</key>
    <true/>
    <key>UISceneConfigurations</key>
    <dict>
        <key>UIWindowSceneSessionRoleApplication</key>
        <array>
            <dict>
                <key>UISceneConfigurationName</key>
                <string>Default Configuration</string>
                <key>UISceneDelegateClassName</key>
                <string>SceneDelegate</string>
                <key>UISceneStoryboardFile</key>
                <string>Main</string>
            </dict>
        </array>
    </dict>
</dict>
```

## <a name="opening-multiple-windows"></a>Ouverture de plusieurs fenêtres

Avec votre application ouverte et en cours d’exécution sur un iPad, il existe plusieurs façons d’ouvrir plusieurs fenêtres de cette application qu’iOS gère par défaut.

- **Exposition** de l’application : Appuyez sur l’icône d’application dans le Dock pour accéder à ce mode lorsque votre application est ouverte.
- **Glissez-faites** glisser l’icône de l’application à partir de l’ancrage en haut de votre application en cours d’exécution pour obtenir une fenêtre flottante.
- **Split Screen** : faites glisser l’icône de l’application du Dock vers le bord de l’écran iPad pour créer une nouvelle fenêtre côte à côte.

Des interactions supplémentaires pour entrer dans un mode à plusieurs fenêtres sont disponibles dans votre application.

**Faire glisser à partir de l’application** : utilisez une interaction de glissement dans `NSUserActivity` votre application pour démarrer une nouvelle icône de glissement d’application dans les exemples précédents.

Lorsque vous utilisez une [interaction de glisser-déplacer][0], vous créez un `NSUserActivity` et associez les données à transmettre à la nouvelle fenêtre que vous demandez à IOS de s’ouvrir pour vous.

```csharp
public UIDragItem [] GetItemsForBeginningDragSession (UICollectionView collectionView, IUIDragSession session, NSIndexPath indexPath)
{
    var selectedPhoto = GetPhoto (indexPath);

    var userActivity = selectedPhoto.OpenDetailUserActivity ();
    var itemProvider = new NSItemProvider (UIImage.FromFile (selectedPhoto.Name));
    itemProvider.RegisterObject (userActivity, NSItemProviderRepresentationVisibility.All);

    var dragItem = new UIDragItem (itemProvider) {
        LocalObject = selectedPhoto
    };

    return new [] { dragItem };
}
```

Dans le code ci-dessus `selectedPhoto` , l’objet de modèle a une méthode `NSUserActivity` pour `OpenDetailUserActivity()`retourner un appelé. Lorsque le mouvement de glissement est terminé `UIDragItem` , le avec `userActivity` présente le `NSItemProvider`via le.

**Actions explicites** : les gestes utilisateur sur des boutons ou des liens offrent la possibilité d’ouvrir une nouvelle fenêtre.

À partir `UIApplication` du, vous pouvez démarrer `UISceneSession` un nouveau `RequestSceneSessionActivation`en appelant. Si une scène existante existe déjà, vous devez l’utiliser. Par défaut, une nouvelle scène est créée pour vous.

```csharp
pubic void ItemSelected(UICollectionView collectionView, NSIndexPath indexPath)
{
    var userActivity = selectedPhoto.OpenDetailUserActivity ();

    UIApplication.SharedApplication.RequestSceneSessionActivation(
        sceneSession: null,
        userActivity: userActivity,
        options: null,
        errorHandler: null
    );
}
```

Dans cet exemple `userActivity` , est la seule valeur passée à la `RequestSceneSessionActivation` méthode pour ouvrir une nouvelle fenêtre de l’application basée sur une action explicite de l’utilisateur `ItemSelected` ; dans ce cas, il s’agit d' `UICollectionView`un gestionnaire d’un.

## <a name="related-links"></a>Liens connexes

- [Glisser-déplacer dans Xamarin. iOS][0]

[0]: ~/ios/platform/introduction-to-ios11/drag-and-drop.md
