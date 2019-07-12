---
title: Migrer une liaison vers l’API unifiée
description: Cet article décrit les étapes nécessaires pour mettre à jour un projet de liaison de Xamarin existant pour prendre en charge les API unifiée pour les applications Xamarin.IOS et Xamarin.Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: f081ccda507fe3fe65af0e2fb50841aecd7b3c23
ms.sourcegitcommit: 654df48758cea602946644d2175fbdfba59a64f3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2019
ms.locfileid: "67830463"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrer une liaison vers l’API unifiée

_Cet article décrit les étapes nécessaires pour mettre à jour un projet de liaison de Xamarin existant pour prendre en charge les API unifiée pour les applications Xamarin.IOS et Xamarin.Mac._

## <a name="overview"></a>Présentation

À partir du 1er février 2015 Apple nécessite que toutes les soumissions de nouveau à l’iTunes et le Store d’application Mac doivent être des applications 64 bits. Par conséquent, toute nouvelle application Xamarin.iOS ou Xamarin.Mac devez être à l’aide de la nouvelle API unifiée au lieu des MonoMac APIs et MonoTouch classique existant pour prendre en charge 64 bits.

En outre, n’importe quel projet de liaison Xamarin doit également en charge les nouvelles API unifiée à inclure dans un 64 bits Xamarin.iOS ou d’un projet Xamarin.Mac. Cet article décrit les étapes requises pour mettre à jour un projet existant de la liaison pour utiliser l’API unifiée.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article :

- **Visual Studio pour Mac** -la dernière version de Visual Studio pour Mac installé et configuré sur l’ordinateur de développement.
- **Apple Mac** - Apple mac est nécessaire pour générer des projets de liaison pour iOS et Mac.

Projets de liaison ne sont pas pris en charge dans Visual studio sur un ordinateur Windows.

## <a name="modify-the-using-statements"></a>Modifier les instructions Using

Les API unifiée rend plus facile que jamais de partager du code entre Mac et iOS ainsi que qui vous permet de prendre en charge 32 et 64 bits des applications avec le même binaire. En supprimant le _MonoMac_ et _MonoTouch_ préfixes des espaces de noms, partage plus simple est possible entre les projets d’application Xamarin.Mac et Xamarin.iOS.

Par conséquent, nous devrons modifier un de nos contrats de liaison (et d’autres `.cs` fichiers dans notre projet de liaison) pour supprimer le _MonoMac_ et _MonoTouch_ préfixes à partir de notre `using` instructions.

Par exemple, étant donné ce qui suit à l’aide d’instructions dans un contrat de liaison :

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Nous serait suppriment le `MonoTouch` préfixe résultant dans le code suivant :

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Là encore, nous devons faire pour tout `.cs` fichier dans notre projet de liaison. Avec cette modification en place, l’étape suivante consiste à mettre à jour notre projet de liaison pour utiliser les nouveaux Types de données natif.

Pour plus d’informations sur l’API unifiée, consultez le [API unifiée](~/cross-platform/macios/unified/index.md) documentation. Pour plus d’informations sur la prise en charge 32 et 64 bits applications et informations sur les infrastructures, consultez le [32 et 64 bits des considérations relatives aux plateformes](~/cross-platform/macios/32-and-64/index.md) documentation.

## <a name="update-to-native-data-types"></a>Mise à jour de Types de données natifs

Objective-C mappe le `NSInteger` type de données à `int32_t` sur les systèmes 32 bits et en `int64_t` sur les systèmes 64 bits. Pour faire correspondre ce comportement, la nouvelle API unifiée remplace les utilisations précédentes de `int` (qui, dans .NET est défini comme étant toujours `System.Int32`) en un nouveau type de données : `System.nint`.

Ainsi que la nouvelle `nint` introduit de type de données, l’API unifiée le `nuint` et `nfloat` types, pour le mappage à la `NSUInteger` et `CGFloat` types également.

Étant donné la méthode ci-dessus, nous devons examiner notre API et vous assurer que toutes les instances de `NSInteger`, `NSUInteger` et `CGFloat` qui nous est déjà mappé à `int`, `uint` et `float` mis à jour vers la nouvelle `nint`, `nuint` et `nfloat` types.

Par exemple, étant donné une définition de méthode Objective-C de :

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Si le contrat de liaison précédente avait la définition suivante :

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Nous mettons à jour la nouvelle liaison soit :

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Si nous avons un mappage vers une bibliothèque de tiers 3e de version plus récente que ce que nous avions initialement lié à, nous devons examiner le `.h` des fichiers d’en-tête pour la bibliothèque et le voir, le cas échéant existants, explicites les appels à `int`, `int32_t`, `unsigned int`, `uint32_t` ou `float` ont été mis à niveau pour être un `NSInteger`, `NSUInteger` ou un `CGFloat`. Dans ce cas, les mêmes modifications à la `nint`, `nuint` et `nfloat` types seront doivent être apportées à leurs mappages également.

Pour en savoir plus sur ces modifications de type de données, consultez le [Types natifs](~/cross-platform/macios/nativetypes.md) document.

## <a name="update-the-coregraphics-types"></a>Mettre à jour les Types de CoreGraphics

Les types de données point, la taille et rectangle qui sont utilisés avec `CoreGraphics` utiliser 32 ou 64 bits en fonction de l’appareil sur lequel ils s’exécutent. Lorsque Xamarin lié à l’origine iOS et Mac API nous avons utilisé des structures de données existantes qui se sont produits pour faire correspondre les types de données dans `System.Drawing` (`RectangleF` par exemple).

En raison de la configuration requise pour prendre en charge 64 bits et les nouveaux types de données natifs, les ajustements suivants seront doivent être apportées au code existant lors de l’appel `CoreGraphic` méthodes :

- **CGRect** -utilisez `CGRect` au lieu de `RectangleF` lorsque la définition flottante point régions rectangulaires.
- **CGSize** -utilisez `CGSize` au lieu de `SizeF` lors de la définition des tailles de point flottant (largeur et hauteur).
- **CGPoint** -utilisez `CGPoint` au lieu de `PointF` lorsque la définition flottante point (coordonnées X et Y).

Étant donné la méthode ci-dessus, nous devons examiner notre API et vérifiez que toutes les instances de `CGRect`, `CGSize` ou `CGPoint` qui a été précédemment lié à `RectangleF`, `SizeF` ou `PointF` être converti en type natif `CGRect`, `CGSize` ou `CGPoint` directement.

Par exemple, étant donné un initialiseur Objective-C de :

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Si notre liaison précédente inclus le code suivant :

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Nous mettons à jour de ce code :

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Toutes les modifications de code étant maintenant en place, nous devons modifier notre projet de liaison ou de rendre le fichier à lier à l’API unifiée.

## <a name="modify-the-binding-project"></a>Modifier le projet de liaison

La dernière étape pour la mise à jour de notre projet de liaison pour utiliser les API unifiée, nous devons modifier la `MakeFile` que nous utilisons pour générer le projet ou le Type de projet Xamarin (si nous effectuons une liaison à partir de Visual Studio pour Mac) et de demander à _btouch_  à lier à l’API unifiée au lieu de celles classique.


### <a name="updating-a-makefile"></a>La mise à jour d’un MakeFile

Si nous utilisons un makefile à générer notre projet de liaison dans un Xamarin. DLL, nous devons inclure le `--new-style` option de ligne de commande et appelez `btouch-native` au lieu de `btouch`.

Par conséquent, étant donné ce qui suit `MakeFile`:

```csharp
BINDDIR=/src/binding
XBUILD=/Applications/Xcode.app/Contents/Developer/usr/bin/xcodebuild
PROJECT_ROOT=XMBindingLibrarySample
PROJECT=$(PROJECT_ROOT)/XMBindingLibrarySample.xcodeproj
TARGET=XMBindingLibrarySample
BTOUCH=/Developer/MonoTouch/usr/bin/btouch


all: XMBindingLibrary.dll

libXMBindingLibrarySample-i386.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphonesimulator -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphonesimulator/lib$(TARGET).a $@

libXMBindingLibrarySample-arm64.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch arm64 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySample-armv7.a:
    $(XBUILD) -project $(PROJECT) -target $(TARGET) -sdk iphoneos -arch armv7 -configuration Release clean build
    -mv $(PROJECT_ROOT)/build/Release-iphoneos/lib$(TARGET).a $@

libXMBindingLibrarySampleUniversal.a: libXMBindingLibrarySample-armv7.a libXMBindingLibrarySample-i386.a libXMBindingLibrarySample-arm64.a
    lipo -create -output $@ $^

XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a

clean:
    -rm -f *.a *.dll
```

Nous devons passer de l’appel `btouch` à `btouch-native`, donc nous serait ajuster notre définition de macro comme suit :

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Nous mettons à jour l’appel à `btouch` et ajoutez le `--new-style` option comme suit :

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Nous pouvons à présent exécuter notre `MakeFile` comme d’habitude pour générer la nouvelle version 64 bits de notre API.

### <a name="updating-a-binding-project-type"></a>La mise à jour d’un Type de projet de liaison

Si nous utilisons un Visual Studio pour Mac liaison de modèle de projet pour concevoir notre API, nous allons devoir mettre à jour vers la nouvelle version d’API unifiée la liaison du modèle de projet. Pour ce faire, le plus simple consiste à démarrer un nouveau projet de liaison d’API unifiée et copie sur l’ensemble du code et des paramètres existants.

Effectuez ce qui suit :

1. Démarrez Visual Studio pour Mac.
2. Sélectionnez **fichier** > **nouveau** > **Solution...**
3. Dans la boîte de dialogue Nouvelle Solution, sélectionnez **iOS** > **API unifiée** > **iOS projet Binding**: 

    [![](update-binding-images/image01new.png "Dans la boîte de dialogue Nouvelle Solution, sélectionnez iOS / API unifiée / projet de liaison iOS")](update-binding-images/image01new.png#lightbox)
4. Dans la boîte de dialogue « Configurer votre nouveau projet » Entrez un **nom** pour le nouveau projet de liaison et cliquez sur le **OK** bouton.
5. Inclure la version 64 bits de la bibliothèque Objective-C que vous allez créer des liaisons pour.
6. Copiez le code source à partir de votre projet de liaison d’API classique 32 bits existant (tel que le `ApiDefinition.cs` et `StructsAndEnums.cs` fichiers).
7. Apportez les modifications d’indiqués ci-dessus pour les fichiers de code source.

Avec toutes ces modifications en place, vous pouvez créer la nouvelle version 64 bits de l’API comme vous le feriez pour la version 32 bits.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons montré les modifications qui doivent être apportées à un projet de liaison Xamarin existant pour prendre en charge les nouvelles API unifiée et les appareils de 64 bits et les étapes requises pour générer la nouvelle version compatible 64 bits d’une API.



## <a name="related-links"></a>Liens associés

- [Mac et iOS](~/cross-platform/macios/index.md)
- [API unifiée](~/cross-platform/macios/nativetypes.md)
- [Considérations relatives à la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [La mise à niveau des applications iOS existantes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unifiée](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://developer.xamarin.com/samples/monotouch/BindingSample/)
