---
title: Référencement de bibliothèques natives dans Xamarin. iOS
description: Ce document explique comment lier des bibliothèques C natives dans une application Xamarin. iOS. Il décrit comment créer des bibliothèques natives universelles et accéder aux méthodes C#C à partir de.
ms.prod: xamarin
ms.assetid: 1DA80280-E78A-EC4B-8673-C249C8425CF5
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 07/28/2016
ms.openlocfilehash: 426b46f578ac3a09372fa4bf63ace1b6545c4866
ms.sourcegitcommit: 6264fb540ca1f131328707e295e7259cb10f95fb
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/16/2019
ms.locfileid: "69528179"
---
# <a name="referencing-native-libraries-in-xamarinios"></a>Référencement de bibliothèques natives dans Xamarin. iOS

Xamarin. iOS prend en charge la liaison avec les bibliothèques C natives et les bibliothèques objective-C. Ce document explique comment lier vos bibliothèques C natives à votre projet Xamarin. iOS. Pour plus d’informations sur la façon de procéder de la même façon pour les bibliothèques objective-C, consultez notre document sur les [types objective-c](~/ios/platform/binding-objective-c/index.md) .

<a name="building_native" />

## <a name="building-universal-native-libraries-i386-armv7-and-arm64"></a>Génération de bibliothèques universelles natives (i386, ARMv7 et ARM64)

Il est souvent souhaitable de créer vos bibliothèques natives pour chacune des plateformes prises en charge pour le développement iOS (i386 pour le simulateur et ARMv7/ARM64 pour les appareils eux-mêmes). Si vous disposez déjà d’un projet XCode pour votre bibliothèque, cette opération est très simple à effectuer.

Pour générer la version i386 de votre bibliothèque native, exécutez la commande suivante à partir d’un terminal:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphonesimulator -arch i386 -configuration Release clean build
```

Cela génère une bibliothèque statique Native sous `MyProject.xcodeproj/build/Release-iphonesimulator/`. Copiez (ou déplacez) le fichier d’archive de la bibliothèque (libMyLibrary. a) dans un endroit sûr pour une utilisation ultérieure, en lui attribuant un nom unique (par exemple, **libMyLibrary-i386. a**) afin qu’il n’entre pas en conflit avec les versions arm64 et ARMv7 de la même bibliothèque que vous allez créer ensuite.

Pour générer la version ARM64 de votre bibliothèque native, exécutez la commande suivante:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch arm64 -configuration Release clean build
```

Cette fois, la bibliothèque native générée se trouvera dans `MyProject.xcodeproj/build/Release-iphoneos/`. Une fois encore, copiez (ou déplacez) ce fichier vers un emplacement sûr, en le renommant comme **libMyLibrary-arm64. a** afin qu’il ne soit pas en conflit.

Générez maintenant la version ARMv7 de la bibliothèque:

```bash
/Developer/usr/bin/xcodebuild -project MyProject.xcodeproj -target MyLibrary -sdk iphoneos -arch armv7 -configuration Release clean build
```

Copiez (ou déplacez) le fichier de bibliothèque résultant vers le même emplacement que celui où vous avez déplacé les deux autres versions de la bibliothèque, en le renommant comme **libMyLibrary-ARMv7. a**.

Pour créer un fichier binaire universel, vous pouvez utiliser `lipo` l’outil comme suit:

```bash
lipo -create -output libMyLibrary.a libMyLibrary-i386.a libMyLibrary-arm64.a libMyLibrary-armv7.a
```

Cela crée `libMyLibrary.a` une bibliothèque universelle (FAT) qui sera utilisable pour toutes les cibles de développement iOS.


### <a name="missing-required-architecture-i386"></a>Architecture requise manquante i386

Si vous obtenez un `does not implement methodSignatureForSelector` message ou `does not implement doesNotRecognizeSelector` dans la sortie de votre Runtime lors de la tentative d’utilisation d’une bibliothèque objective-C dans le simulateur iOS, votre bibliothèque n’a probablement pas été compilée pour l’architecture i386 (voir la [génération Universal Native Section bibliothèques](#building_native) ci-dessus).

Pour vérifier les architectures prises en charge par une bibliothèque donnée, utilisez la commande suivante dans le terminal:

```bash
lipo -info /full/path/to/libraryname.a
```

Où `/full/path/to/` est le chemin d’accès complet à la bibliothèque qui `libraryname.a` est consommé et est le nom de la bibliothèque en question.

Si vous avez la source à la bibliothèque, vous devez la compiler et la regrouper pour l’architecture i386, si vous souhaitez tester votre application sur le simulateur iOS.

### <a name="linking-your-library"></a>Liaison de votre bibliothèque

Toute bibliothèque tierce que vous consommez doit être liée de manière statique à votre application. 

Si vous souhaitez lier de manière statique la bibliothèque «libMyLibrary. a» que vous avez obtenue à partir d’Internet ou créer avec Xcode, vous devez effectuer quelques opérations:

- Placer la bibliothèque dans votre projet
- Configurer Xamarin. iOS pour lier la bibliothèque
- Accédez aux méthodes à partir de la bibliothèque.


Pour **Placer la bibliothèque dans votre projet**, sélectionnez le projet dans l’Explorateur de solutions et appuyez sur **commande + option + a**. Accédez à libMyLibrary. a et ajoutez-le au projet. Quand vous y êtes invité, indiquez à Visual Studio pour Mac ou Visual Studio de le copier dans le projet. Après l’avoir ajouté, recherchez libFoo. a dans le projet, cliquez avec le bouton droit sur celui-ci et définissez l' **action de génération** sur **aucun**.

Pour **configurer Xamarin. iOS afin de lier la bibliothèque**, sur les options du projet pour votre exécutable final (et non dans la bibliothèque elle-même, mais dans le dernier programme), vous devez ajouter l’argument supplémentaire de la **Build iOS**(ce qui fait partie des options de votre projet) «-gcc_flags» option suivie d’une chaîne entre guillemets qui contient toutes les bibliothèques supplémentaires requises pour votre programme, par exemple:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -force_load ${ProjectDir}/libMyLibrary.a"
```

L’exemple ci-dessus permet **de lier libMyLibrary. a**

Vous pouvez utiliser `-gcc_flags` pour spécifier un jeu d’arguments de ligne de commande à passer au compilateur GCC utilisé pour effectuer le lien final de votre exécutable. Par exemple, cette ligne de commande référence également l’infrastructure CFNetwork:

```bash
-gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

Si votre bibliothèque Native contient C++ du code, vous devez également transmettre l’indicateur-cxx dans vos «arguments supplémentaires» pour que Xamarin. iOS sache utiliser le compilateur approprié. Les C++ options précédentes ressemblent à ceci:

```bash
-cxx -gcc_flags "-L${ProjectDir} -lMylibrary -lSystemLibrary -framework CFNetwork -force_load ${ProjectDir}/libMyLibrary.a"
```

<a name="Accessing_C_Methods_from_C#" />

## <a name="accessing-c-methods-from-c35"></a>Accès aux méthodes C à partir de C&#35;

Deux types de bibliothèques natives sont disponibles sur iOS:

- Bibliothèques partagées qui font partie du système d’exploitation.

- Bibliothèques statiques que vous fournissez avec votre application.


Pour accéder aux méthodes définies dans l’un d’eux, vous utilisez la [fonctionnalité P/Invoke de mono](https://www.mono-project.com/docs/advanced/pinvoke/) , qui est la même technologie que celle que vous utiliseriez dans .net, ce qui est à peu près:

- Déterminer la fonction C que vous souhaitez appeler
- Déterminer sa signature
- Déterminer la bibliothèque dans laquelle elle réside
- Écrire la déclaration P/Invoke appropriée

Quand vous utilisez P/Invoke, vous devez spécifier le chemin d’accès de la bibliothèque avec laquelle vous établissez la liaison. Lorsque vous utilisez des bibliothèques partagées iOS, vous pouvez soit coder en dur le chemin d’accès, soit utiliser les constantes pratiques que `Constants`nous avons définies dans notre, ces constantes doivent couvrir les bibliothèques partagées iOS.

Par exemple, si vous souhaitez appeler la méthode UIRectFrameUsingBlendMode à partir de la bibliothèque UIKit d’Apple qui a cette signature en C:

```csharp
void UIRectFrameUsingBlendMode (CGRect rect, CGBlendMode mode);
```

Votre déclaration P/Invoke ressemble à ceci:

```csharp
[DllImport (Constants.UIKitLibrary,EntryPoint="UIRectFrameUsingBlendMode")]
public extern static void RectFrameUsingBlendMode (RectangleF rect, CGBlendMode blendMode);
```

Constants. UIKitLibrary est simplement une constante définie en tant que «/System/Library/Frameworks/UIKit.framework/UIKit», le point d’entrée nous permet de spécifier éventuellement le nom externe (UIRectFramUsingBlendMode) tout en exposant un C#nom différent dans, le RectFrameUsingBlendMode plus courts.

<a name="Accessing_C_Dylibs" />

### <a name="accessing-c-dylibs"></a>Accès à C dylibs

Si vous avez besoin de consommer un dylib C dans votre application Xamarin. iOS, il y a un peu d’installation supplémentaire nécessaire avant d’appeler l' `DllImport` attribut.

Par exemple, si nous avons un `Animal.dylib` avec une `Animal_Version` méthode que nous appellerons dans notre application, nous devons informer Xamarin. IOS de l’emplacement de la bibliothèque avant de tenter de l’utiliser.

Pour ce faire, modifiez le `Main.CS` fichier et faites en sorte qu’il ressemble à ce qui suit:

```csharp
static void Main (string[] args)
{
    // Load Dylib
    MonoTouch.ObjCRuntime.Dlfcn.dlopen ("/full/path/to/Animal.dylib", 0);

    // Start application
    UIApplication.Main (args, null, "AppDelegate");
}
```

Où `/full/path/to/` est le chemin d’accès complet au dylib consommé. Une fois ce code en place, nous pouvons créer un lien `Animal_Version` vers la méthode comme suit:

```csharp
[DllImport("Animal.dylib", EntryPoint="Animal_Version")]
public static extern double AnimalLibraryVersion();
```

<a name="Static_Libraries" />

### <a name="static-libraries"></a>Bibliothèques statiques

Étant donné que vous ne pouvez utiliser que des bibliothèques statiques sur iOS, il n’existe aucune bibliothèque partagée externe avec laquelle établir une liaison. le paramètre Path de l’attribut `__Internal` DllImport doit donc utiliser le nom spécial (Notez le double trait de soulignement au début du nom), par opposition à nom du chemin d’accès.

Cela force DllImport à rechercher le symbole de la méthode que vous référencez dans le programme actuel, au lieu de tenter de le charger à partir d’une bibliothèque partagée.

