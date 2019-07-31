---
title: Migrer une liaison vers l’API unifiée
description: Cet article décrit les étapes nécessaires pour mettre à jour un projet de liaison Xamarin existant afin de prendre en charge les API unifiées pour les applications Xamarin. IOS et Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5E2A3251-D17F-4F9C-9EA0-6321FEBE8577
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 4046dcff5cb572890ad41ab57efe6345d09f61fd
ms.sourcegitcommit: 3ea9ee034af9790d2b0dc0893435e997bd06e587
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/30/2019
ms.locfileid: "68646297"
---
# <a name="migrating-a-binding-to-the-unified-api"></a>Migrer une liaison vers l’API unifiée

_Cet article décrit les étapes nécessaires pour mettre à jour un projet de liaison Xamarin existant afin de prendre en charge les API unifiées pour les applications Xamarin. IOS et Xamarin. Mac._

## <a name="overview"></a>Présentation

À partir du 1er février, 2015 Apple exige que toutes les nouvelles soumissions à iTunes et au Mac App Store doivent être des applications 64 bits. Par conséquent, toute nouvelle application Xamarin. iOS ou Xamarin. Mac devra utiliser la nouvelle API unifiée au lieu des API MonoTouch et MonoMac classiques existantes pour prendre en charge 64 bits.

En outre, tout projet de liaison Xamarin doit également prendre en charge les nouvelles API unifiées à inclure dans un projet 64 bits Xamarin. iOS ou Xamarin. Mac. Cet article décrit les étapes nécessaires à la mise à jour d’un projet de liaison existant pour utiliser l’API unifiée.

## <a name="requirements"></a>Configuration requise

Les éléments suivants sont requis pour effectuer les étapes présentées dans cet article:

- **Visual Studio pour Mac** : la dernière version de Visual Studio pour Mac installée et configurée sur l’ordinateur de développement.
- **Mac Apple** : un Mac Apple est nécessaire pour créer des projets de liaison pour iOS et Mac.

Les projets de liaison ne sont pas pris en charge dans Visual Studio sur un ordinateur Windows.

## <a name="modify-the-using-statements"></a>Modifier les instructions using

Les API unifiées facilitent plus que jamais le partage de code entre Mac et iOS, ainsi que la prise en charge d’applications 32 et 64 bits avec le même binaire. En supprimant les préfixes _MonoMac_ et unitouch des espaces de noms, un partage plus simple est réalisé entre les projets d’application Xamarin. Mac et Xamarin. iOS.

Par conséquent, nous aurons besoin de modifier l’un de nos contrats de liaison ( `.cs` et d’autres fichiers dans notre projet de liaison) pour supprimer les préfixes _MonoMac_ et `using` unitouch de nos instructions.

Par exemple, étant donné les instructions using suivantes dans un contrat de liaison:

```csharp
using System;
using System.Drawing;
using MonoTouch.Foundation;
using MonoTouch.UIKit;
using MonoTouch.ObjCRuntime;
```

Nous allons supprimer le `MonoTouch` préfixe de ce qui suit:

```csharp
using System;
using System.Drawing;
using Foundation;
using UIKit;
using ObjCRuntime;
```

Là encore, nous devons effectuer cette opération pour tous `.cs` les fichiers de notre projet de liaison. Une fois cette modification en place, l’étape suivante consiste à mettre à jour notre projet de liaison pour utiliser les nouveaux types de données natifs.

Pour plus d’informations sur le API unifiée, consultez la documentation [API unifiée](~/cross-platform/macios/unified/index.md) . Pour plus d’informations sur la prise en charge des applications 32 et 64 bits et des informations sur les frameworks, consultez la documentation sur les [plateformes 32 et 64 bits](~/cross-platform/macios/32-and-64/index.md) .

## <a name="update-to-native-data-types"></a>Mettre à jour les types de données natifs

Objective-C mappe `NSInteger` le type de `int32_t` données à sur les systèmes `int64_t` 32 bits et sur les systèmes 64 bits. Pour correspondre à ce comportement, la nouvelle API unifiée remplace les utilisations précédentes `int` de (qui dans .net est définie comme `System.Int32`toujours) à un nouveau type de données `System.nint`:.

En même temps que `nint` le nouveau type de données, le `nuint` API unifiée `nfloat` présente les types et pour le `NSUInteger` mappage `CGFloat` aux types et.

Compte tenu de ce qui précède, nous devons passer en revue notre API et `NSInteger`vérifier `NSUInteger` que `CGFloat` toutes les instances de et `float` que `int`nous `uint` avons précédemment mappées à la `nint`nouvelle, `nuint` types et`nfloat` .

Par exemple, étant donné une définition de méthode objective-C de:

```csharp
-(NSInteger) add:(NSInteger)operandUn and:(NSInteger) operandDeux;
```

Si le contrat de liaison précédent comportait la définition suivante:

```csharp
[Export("add:and:")]
int Add(int operandUn, int operandDeux);
```

Nous mettons à jour la nouvelle liaison pour qu’elle soit:

```csharp
[Export("add:and:")]
nint Add(nint operandUn, nint operandDeux);
```
Si nous effectuons un mappage vers une bibliothèque de version tierce plus récente que celle que nous avons initialement liée, nous devons `.h` examiner les fichiers d’en-tête pour la bibliothèque et voir si des appels `int`explicites `unsigned int`à `uint32_t` , `int32_t`, ou ont été mis à niveau pour `NSInteger`être `NSUInteger` ou `CGFloat`. `float` Si c’est le cas, les mêmes `nint`modifications `nuint` apportées aux types et `nfloat` doivent également être apportées à leurs mappages.

Pour en savoir plus sur ces modifications de types de données, consultez le document [types natifs](~/cross-platform/macios/nativetypes.md) .

## <a name="update-the-coregraphics-types"></a>Mettre à jour les types CoreGraphics

Types de données de point, de taille et de rectangle utilisés `CoreGraphics` avec les bits 32 ou 64, en fonction de l’appareil sur lequel ils s’exécutent. Lorsque Xamarin a lié à l’origine les API iOS et Mac, nous avons utilisé des structures de données existantes qui `System.Drawing` correspondent`RectangleF` aux types de données dans (par exemple).

En raison de la configuration requise pour prendre en charge 64 bits et les nouveaux types de données natifs, les ajustements suivants doivent être apportés au code existant lors de l’appel `CoreGraphic` de méthodes:

- **CGRect** : à `CGRect` utiliser au `RectangleF` lieu de lors de la définition de régions rectangulaires à virgule flottante.
- **CGSize** : à `CGSize` utiliser au `SizeF` lieu de lors de la définition de tailles à virgule flottante (largeur et hauteur).
- **CGPoint** : à `CGPoint` utiliser au `PointF` lieu de lors de la définition d’un emplacement à virgule flottante (coordonnées X et Y).

Étant donné ce qui précède, nous devons passer en revue notre API et vérifier que `CGRect`toute `CGSize` instance `CGPoint` de, ou précédemment liée `RectangleF`, `SizeF` ou `PointF` être remplacée par le type `CGRect`natif. `CGSize` ou`CGPoint` directement.

Par exemple, en fonction d’un initialiseur objective-C de:

```csharp
- (id)initWithFrame:(CGRect)frame;
```

Si la liaison précédente comprenait le code suivant:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (RectangleF frame);

```

Nous mettons à jour ce code pour effectuer les opérations suivantes:

```csharp
[Export ("initWithFrame:")]
IntPtr Constructor (CGRect frame);

```

Avec toutes les modifications de code actuellement en place, nous devons modifier notre projet de liaison ou créer un fichier à lier aux API unifiées.

## <a name="modify-the-binding-project"></a>Modifier le projet de liaison

Comme dernière étape de la mise à jour de notre projet de liaison pour utiliser les API unifiées, nous devons `MakeFile` soit modifier le que nous utilisons pour générer le projet, soit le type de projet Xamarin (si nous créons une liaison à partir de Visual Studio pour Mac) et ordonner à _btouch_ de se lier contre les API unifiées au lieu des API classiques.


### <a name="updating-a-makefile"></a>Mise à jour d’un MakeFile

Si nous utilisons un makefile pour générer notre projet de liaison dans un Xamarin. DLL, vous devez inclure l' `--new-style` option de ligne de commande et appeler `btouch-native` au lieu de. `btouch`

Par conséquent, étant `MakeFile`donné ce qui suit:

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

Nous devons passer de l’appel `btouch` à `btouch-native`, donc nous ajusterons notre définition de macro comme suit:

```csharp
BTOUCH=/Developer/MonoTouch/usr/bin/btouch-native
```

Nous allons mettre à jour l' `btouch` appel à et `--new-style` ajouter l’option comme suit:

```csharp
XMBindingLibrary.dll: AssemblyInfo.cs XMBindingLibrarySample.cs extras.cs libXMBindingLibrarySampleUniversal.a
    $(BTOUCH) -unsafe --new-style -out:$@ XMBindingLibrarySample.cs -x=AssemblyInfo.cs -x=extras.cs --link-with=libXMBindingLibrarySampleUniversal.a,libXMBindingLibrarySampleUniversal.a
```

Nous pouvons maintenant exécuter notre `MakeFile` version normale pour créer la nouvelle version 64 bit de notre API.

### <a name="updating-a-binding-project-type"></a>Mise à jour d’un type de projet de liaison

Si nous utilisons un modèle de projet de liaison Visual Studio pour Mac pour générer notre API, nous devons effectuer une mise à jour vers la nouvelle version de API unifiée du modèle de projet de liaison. Le moyen le plus simple consiste à démarrer un nouveau API unifiée projet de liaison et à copier tous les paramètres et le code existants.

Effectuez ce qui suit :

1. Démarrez Visual Studio pour Mac.
2. Sélectionner un **fichier** > **nouvelle** > **solution...**
3. Dans la boîte de dialogue nouvelle solution, sélectionnez **iOS** > **API unifiée** > **projet de liaison iOS**: 

    [![](update-binding-images/image01new.png "Dans la boîte de dialogue nouvelle solution, sélectionnez le projet de liaison iOS/API unifiée/iOS.")](update-binding-images/image01new.png#lightbox)
4. Dans la boîte de dialogue «Configurer votre nouveau projet», entrez un **nom** pour le nouveau projet de liaison, puis cliquez sur le bouton **OK** .
5. Incluez la version 64 bits de la bibliothèque objective-C pour laquelle vous allez créer des liaisons.
6. Copiez le code source à partir de votre projet de liaison API classique bits 32 existant ( `ApiDefinition.cs` par `StructsAndEnums.cs` exemple, les fichiers et).
7. Apportez les modifications indiquées ci-dessus aux fichiers de code source.

Une fois toutes ces modifications en place, vous pouvez créer la nouvelle version 64 bit de l’API comme vous le feriez avec la version 32 bits.

## <a name="summary"></a>Récapitulatif

Dans cet article, nous avons présenté les modifications qui doivent être apportées à un projet de liaison Xamarin existant pour prendre en charge les nouvelles API unifiées et les périphériques 64 bits, ainsi que les étapes requises pour créer la nouvelle version compatible 64 bits d’une API.



## <a name="related-links"></a>Liens associés

- [Mac et iOS](~/cross-platform/macios/index.md)
- [API unifiée](~/cross-platform/macios/nativetypes.md)
- [Considérations relatives à la plateforme 32/64 bits](~/cross-platform/macios/32-and-64/index.md)
- [Mise à niveau des applications iOS existantes](~/cross-platform/macios/unified/updating-ios-apps.md)
- [API unifiée](~/cross-platform/macios/unified/index.md)
- [BindingSample](https://docs.microsoft.com/en-us/samples/xamarin/ios-samples/bindingsample/)
