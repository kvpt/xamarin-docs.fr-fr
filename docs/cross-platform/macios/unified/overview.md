---
title: Présentation de API unifiée
description: Le API unifiée de Xamarin permet de partager du code entre Mac et iOS et de prendre en charge les applications 32 et 64 bits avec le même binaire.
ms.prod: xamarin
ms.assetid: 5F0CEC18-5EF6-4A99-9DCF-1A3B57EA157C
author: asb3993
ms.author: amburns
ms.date: 03/29/2017
ms.openlocfilehash: 9d36101c1416ea8ddf451f5677258972c4f34990
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68511135"
---
# <a name="unified-api-overview"></a>Présentation de API unifiée

Le API unifiée de Xamarin permet de partager du code entre Mac et iOS et de prendre en charge les applications 32 et 64 bits avec le même binaire. La API unifiée est utilisée par défaut dans les nouveaux projets Xamarin. iOS et Xamarin. Mac.

> [!IMPORTANT]
> Le API classique Xamarin, qui précédait le API unifiée, est déconseillé. 
> - La dernière version de Xamarin. iOS pour la prise en charge de l’API classique (unitouch. dll) était Xamarin. iOS 9,10.
> - Xamarin. Mac prend toujours en charge le API classique, mais il n’est plus mis à jour. Étant donné qu’il est déconseillé, les développeurs doivent déplacer leurs applications vers le API unifiée.

## <a name="updating-classic-api-based-apps"></a>Mise à jour des applications basées sur des API classique

Suivez les instructions pertinentes pour votre plateforme:

- [Mettre à jour des applications existantes](updating-apps.md)
- [Mettre à jour des applications iOS existantes](updating-ios-apps.md)
- [Mettre à jour des applications Mac existantes](updating-mac-apps.md)
- [Mettre à jour des applications Xamarin.Forms existantes](updating-xamarin-forms-apps.md)
- [Migrer une liaison vers l’API unifiée](update-binding.md)

## <a name="tips-for-updating-code-to-the-unified-apiupdating-tipsmd"></a>[Conseils pour la mise à jour du code vers l’API unifiée](updating-tips.md)

Quelles que soient les applications que vous migrez, consultez [ces conseils](updating-tips.md) pour vous aider à mettre à jour avec succès la API unifiée.

## <a name="library-split"></a>Fractionnement de bibliothèque

À partir de ce point, nos API seront exposées de deux manières:

-  **API classique:** Limité à 32 bits (uniquement) et exposés dans les `monotouch.dll` assemblys et. `XamMac.dll`
-  **API unifiée:** Prenez en charge le développement 32 et 64 bits avec une seule API disponible `Xamarin.iOS.dll` dans `Xamarin.Mac.dll` les assemblys et.

Cela signifie que pour les développeurs d’entreprise (ne ciblant pas l’App Store), vous pouvez continuer à utiliser les API classiques existantes, car nous allons toujours les conserver à jour, ou vous pouvez effectuer une mise à niveau vers les nouvelles API.

<a name="namespace-changes" />

## <a name="namespace-changes"></a>Modifications des espaces de noms

Pour réduire le frottement afin de partager du code entre nos produits Mac et iOS, nous modifions les espaces de noms des API dans les produits.

Nous supprimons le préfixe «unitouch» de notre produit iOS et «MonoMac» de notre produit Mac sur les types de données.

Il est ainsi plus simple de partager du code entre les plateformes Mac et iOS sans avoir recours à la compilation conditionnelle et réduira le bruit en haut de vos fichiers de code source.

-  **API classique:** Les espaces de `MonoTouch.` noms `MonoMac.` utilisent ou prefix.
-  **API unifiée:** Aucun préfixe d’espace de noms

## <a name="runtime-defaults"></a>Valeurs par défaut du Runtime

Par défaut, le API unifiée utilise le garbage collector **SGen** et le nouveau système de [comptage de références](~/ios/internals/newrefcount.md) pour le suivi de la propriété de l’objet. Cette même fonctionnalité a été reportée vers Xamarin. Mac.

Cela résout un certain nombre de problèmes que les développeurs ont rencontrés avec l’ancien système et facilitent également la gestion de la [mémoire](~/cross-platform/deploy-test/memory-perf-best-practices.md).

Notez qu’il est possible d’activer un nouveau refcount même pour le API classique, mais la valeur par défaut est conservatrice et ne nécessite pas que les utilisateurs effectuent des modifications. Avec la API unifiée, nous avons pris la possibilité de modifier la valeur par défaut et de fournir aux développeurs toutes les améliorations en même temps qu’ils refactorisaient et testent à nouveau leur code.

## <a name="api-changes"></a>Modifications de l’API

Le API unifiée supprime les méthodes déconseillées et il existe quelques cas où il y avait des fautes de frappe dans les noms d’API lorsqu’ils étaient liés aux espaces de noms monotactiles et MonoMac d’origine dans les API classiques. Ces instances ont été corrigées dans les nouvelles API unifiées et devront être mises à jour dans vos applications de composant, iOS et Mac. Voici la liste des éléments les plus courants que vous pouvez rencontrer:

|Nom de la méthode API classique|Nom de la méthode API unifiée|
|--- |--- |
|`UINavigationController.PushViewControllerAnimated()`|`UINavigationController.PushViewController()`|
|`UINavigationController.PopViewControllerAnimated()`|`UINavigationController.PopViewController()`|
|`CGContext.SetRGBFillColor()`|`CGContext.SetFillColor()`|
|`NetworkReachability.SetCallback()`|`NetworkReachability.SetNotification()`|
|`CGContext.SetShadowWithColor`|`CGContext.SetShadow`|
|`UIView.StringSize`|`UIKit.UIStringDrawing.StringSize`|

Pour obtenir une liste complète des modifications apportées lors du passage du modèle classique au API unifiée, consultez la documentation sur les différences de l' [API classique (MonoTouch. dll) et Unified (Xamarin. iOS. dll)](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) .

## <a name="updating-to-unified"></a>Mise à jour vers Unified

Plusieurs API obsolètes/cassées/déconseillées dans **Classic** ne sont pas disponibles dans l’API **unifiée** . Il peut être plus facile de corriger `CS0616` les avertissements avant de commencer la mise à niveau (manuelle ou automatisée) `[Obsolete]` puisque vous aurez le message d’attribut (une partie de l’avertissement) pour vous guider vers l’API appropriée.

Notez que nous publions une [*différence*](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md) entre les modifications de l’API unifiée vs classiques qui peuvent être utilisées avant ou après les mises à jour de votre projet. La résolution continue des appels obsolètes dans Classic est souvent un économiseur de temps (moins de recherches dans la documentation).

Suivez ces instructions pour [mettre à jour les applications iOS existantes](~/cross-platform/macios/unified/updating-ios-apps.md)ou les [applications Mac](~/cross-platform/macios/unified/updating-mac-apps.md) vers le API unifiée.
Consultez le reste de cette page et [ces conseils](~/cross-platform/macios/unified/updating-tips.md) pour plus d’informations sur la migration de votre code.

### <a name="nuget"></a>NuGet

Les packages NuGet qui ont précédemment pris en charge Xamarin. iOS via le API classique publié leurs assemblys à l’aide du moniker de plateforme **Monotouch10** .

Le API unifiée introduit un nouvel identificateur de plateforme pour les packages compatibles- **Xamarin. iOS10**. Les packages NuGet existants devront être mis à jour pour ajouter la prise en charge de cette plateforme, en générant des API unifiée.

> [!IMPORTANT]
> Si vous avez une erreur au format _«l’erreur 3 ne peut pas inclure à la fois «MonoTouch. dll» et «Xamarin. iOS. dll» dans le même projet Xamarin. iOS-«Xamarin. iOS. dll» est référencé explicitement, tandis que «unitouch. dll» est référencé par «xxx, version = 0.0.000, culture = neutral, PublicKeyToken = null'»_ après la conversion de votre application en API unifiées, il est généralement nécessaire de disposer d’un composant ou d’un package NuGet dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant/NuGet existant, effectuer une mise à jour vers une version qui prend en charge les API unifiées et effectuer une génération propre.

### <a name="the-road-to-64-bits"></a>La route à 64 bits

Pour plus d’informations sur la prise en charge des applications 32 et 64 bits et des informations sur les frameworks, voir considérations sur la [plateforme 32 et 64 bits](~/cross-platform/macios/32-and-64/index.md).

 <a name="new-data-types" />

#### <a name="new-data-types"></a>Nouveaux types de données

Au cœur de la différence, les API Mac et iOS utilisent des types de données spécifiques à l’architecture qui sont toujours 32 bits sur les plateformes 32 bits et 64 bits sur les plateformes 64 bits.

Par exemple, Objective-C mappe `NSInteger` le type de `int32_t` données sur sur les systèmes bits `int64_t` 32 et sur les systèmes 64 bits.

Pour correspondre à ce comportement, sur notre API unifiée, nous remplaçons les utilisations précédentes `int` de (qui dans .net est définie comme `System.Int32`toujours) à un nouveau type de données `System.nint`:.  Vous pouvez considérer «n» comme «native», donc le type d’entier natif de la plateforme.

Nous introduisons `nint`et `nuint` `nfloat` fournissons également des types de données qui s’appuient sur eux, le cas échéant.

Pour en savoir plus sur ces modifications de types de données, consultez le document [types natifs](~/cross-platform/macios/nativetypes.md) .

### <a name="how-to-detect-the-architecture-of-ios-apps"></a>Comment détecter l’architecture des applications iOS

Il peut arriver que votre application ait besoin de savoir si elle s’exécute sur un système iOS 32 ou 64 bits. Le code suivant peut être utilisé pour vérifier l’architecture:

```csharp
if (IntPtr.Size == 4) {
    Console.WriteLine ("32-bit App");
} else if (IntPtr.Size == 8) {
    Console.WriteLine ("64-bit App");
}
```

<a name="deprecated-apis" />

### <a name="arrays-and-systemcollectionsgeneric"></a>Tableaux et System. Collections. Generic

Étant C# donné que les indexeurs attendent `int`un type de, vous devrez effectuer `nint` un cast `int` explicite des valeurs vers pour accéder aux éléments d’une collection ou d’un tableau. Par exemple :

```csharp
public List<string> Names = new List<string>();
...

public string GetName(nint index) {
    return Names[(int)index];
}

```

Ce comportement est attendu, car le cast de `int` en `nint` est perdu sur 64 bit, une conversion implicite n’est pas effectuée.

### <a name="converting-datetime-to-nsdate"></a>Conversion de DateTime en NSDate

Lors de l’utilisation des API unifiées, la `DateTime` conversion `NSDate` implicite de en valeurs n’est plus effectuée. Ces valeurs doivent être explicitement converties d’un type à un autre. Les méthodes d’extension suivantes peuvent être utilisées pour automatiser ce processus:

```csharp
public static DateTime NSDateToDateTime(this NSDate date)
{
    // NSDate has a wider range than DateTime, so clip
    // the converted date to DateTime.Min|MaxValue.
    double secs = date.SecondsSinceReferenceDate;
    if (secs < -63113904000)
        return DateTime.MinValue;
    if (secs > 252423993599)
        return DateTime.MaxValue;
    return (DateTime) date;
}

public static NSDate DateTimeToNSDate(this DateTime date)
{
    if (date.Kind == DateTimeKind.Unspecified)
        date = DateTime.SpecifyKind (date, /* DateTimeKind.Local or DateTimeKind.Utc, this depends on each app */)
    return (NSDate) date;
}

```

<a name="deprecated-typos" />

### <a name="deprecated-apis-and-typos"></a>API déconseillées et fautes de frappe

Dans l’API classique Xamarin. iOS, l' `[Obsolete]` attribut a été utilisé de deux manières différentes:

-  **API iOS déconseillée:** C’est le cas lorsqu’Apple vous indique d’arrêter d’utiliser une API, car elle est remplacée par une version plus récente. Le API classique est toujours parfait et souvent requis (si vous prenez en charge l’ancienne version d’iOS).
 Ces API (et l' `[Obsolete]` attribut) sont incluses dans les nouveaux assemblys Xamarin. iOS.
-  **API** incorrecte Certaines API avaient des fautes de frappe sur leurs noms.

Pour les assemblys d’origine (MonoTouch. dll et XamMac. dll), nous avons conservé l’ancien code disponible pour la compatibilité, mais ils ont été supprimés de la API unifiée assemblys (Xamarin. iOS. dll et Xamarin. Mac).

<a name="NSObject_ctor" />

### <a name="nsobject-subclasses-ctorintptr"></a>NSObject sous-classes. ctor (IntPtr)

Chaque `NSObject` sous-classe a un constructeur qui accepte `IntPtr`un. C’est ainsi que nous pouvons instancier une nouvelle instance gérée à partir d’un handle ObjC natif.

Dans le modèle classique, `public` il s’agissait d’un constructeur. Toutefois, il était facile d’utiliser cette fonctionnalité dans le code utilisateur, par exemple la création de plusieurs instances gérées pour une instance ObjC unique *ou* la création d’une instance gérée qui n’aurait pas l’état managé attendu (pour les sous-classes).

Pour éviter ces types de problèmes, `IntPtr` les constructeurs sont désormais `protected` dans  une API unifiée, à utiliser uniquement pour la sous-classe. Cela permet de s’assurer que l’API correcte/sécurisée est utilisée pour créer une instance gérée à partir de handles, c.-à-d.

    var label = Runtime.GetNSObject<UILabel> (handle);

Cette API retourne une instance gérée existante (si elle existe déjà) ou en crée une (si nécessaire). Il est déjà disponible dans les API classiques et unifiées.

Notez que le `.ctor(NSObjectFlag)` est maintenant également `protected` , mais il était rarement utilisé en dehors de la sous-classe.

<a name="NSAction" />

### <a name="nsaction-replaced-with-action"></a>NSAction remplacé par action

Avec les API unifiées `NSAction` , a été supprimé en faveur de .NET `Action`standard. Il s’agit d’une amélioration `Action` importante, car est un type .NET `NSAction` courant, alors que était spécifique à Xamarin. iOS. Ils font tous les deux exactement la même chose, mais ils ont des types distincts et incompatibles et ont entraîné l’écriture d’un plus grand nombre de code pour obtenir le même résultat.

Par exemple, si votre application Xamarin existante incluait le code suivant:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (new NSAction (delegate() {
    ShowDropDownAnimated (tblDataView);
}));
```
Il peut désormais être remplacé par une expression lambda simple:

```csharp
UITapGestureRecognizer singleTap = new UITapGestureRecognizer (() => ShowDropDownAnimated(tblDataView));
```

Auparavant, il s’agit d’une erreur du `Action` compilateur, car une `NSAction`ne peut pas être assignée à, `NSAction` mais depuis que `UITapGestureRecognizer` prend maintenant un `Action` au lieu d’un, il est valide dans les API unifiées.

### <a name="custom-delegates-replaced-with-actiont"></a>Délégués personnalisés remplacés par l’action<T>

Dans **Unified** some simple (par exemple, un paramètre), les délégués `Action<T>`.net ont été remplacés par. Par exemple,

    public delegate void NSNotificationHandler (NSNotification notification);

peut maintenant être utilisé en tant `Action<NSNotification>`que. Cela favorise la réutilisation du code et réduit la duplication du code à la fois dans Xamarin. iOS et vos propres applications.

### <a name="taskbool-replaced-with-taskbooleannserror"></a>Tâche<bool> remplacée par Task < Boolean, nserror > >

En mode **classique** , certaines API Async retournent `Task<bool>`. Toutefois, certaines d’entre elles doivent être utilisées quand `NSError` un faisait partie de la signature, c.-à-d `bool` . que a `true` déjà été et que vous `NSError`deviez intercepter une exception pour récupérer.

Étant donné que certaines erreurs sont très courantes et que la valeur de retour n’est pas utile  , ce modèle a `Task<Tuple<Boolean,NSError>>`été modifié dans Unified pour retourner un. Cela vous permet de vérifier à la fois la réussite et toute erreur qui peut s’être produite pendant l’appel asynchrone.

### <a name="nsstring-vs-string"></a>Chaîne NSString et chaîne

Dans certains cas, certaines constantes devaient être modifiées de `string` en `NSString`, par exemple`UITableViewCell`

**Classic**

    public virtual string ReuseIdentifier { get; }

**Unifié**

    public virtual NSString ReuseIdentifier { get; }

En général, nous préférons `System.String` le type .net. Toutefois, malgré les instructions Apple, certaines API natives sont en comparant des pointeurs constants (pas la chaîne elle-même) et cela ne fonctionne `NSString`que lorsque nous exposez les constantes comme.

 <a name="protocols" />

### <a name="objective-c-protocols"></a>Protocoles objective-C

Le monocontact d’origine n’offrait pas de prise en charge complète des protocoles ObjC et certaines API non optimales ont été ajoutées pour prendre en charge le scénario le plus courant. Cette limitation n’existe plus, mais, pour des raisons de compatibilité descendante, plusieurs API `monotouch.dll` sont `XamMac.dll`conservées à l’intérieur et.

Ces limitations ont été supprimées et nettoyées sur les API unifiées. La plupart des modifications se présentent comme suit:

**Classic**

    public virtual AVAssetResourceLoaderDelegate Delegate { get; }

**Unifié**

    public virtual IAVAssetResourceLoaderDelegate Delegate { get; }

Le `I` préfixe  signifie que la méthode Unified expose une interface, au lieu d’un type spécifique, pour le protocole objc. Cela facilite les cas où vous ne souhaitez pas sous-classer le type spécifique fourni par Xamarin. iOS.

Il permettait également à certaines API d’être plus précises et faciles à utiliser, par exemple:

**Classic**

    public virtual void SelectionDidChange (NSObject uiTextInput);

**Unifié**

    public virtual void SelectionDidChange (IUITextInput uiTextInput);

De telles API sont désormais plus faciles à nous, sans référence à la documentation, et la saisie semi-automatique de votre code IDE vous fournira des suggestions plus utiles basées sur le protocole/l’interface.

#### <a name="nscoding-protocol"></a>Protocole NSCoding

Notre liaison d’origine incluait un. ctor (NSCoder) pour chaque type, même s’il ne prenait `NSCoding` pas en charge le protocole.  Une méthode `Encode(NSCoder)` unique était présente dans le `NSObject` pour encoder l’objet.
Toutefois, cette méthode fonctionne uniquement si l’instance est conforme au protocole NSCoding.

Sur le API unifiée nous avons résolu ce problème.  Les nouveaux assemblys auront uniquement la `.ctor(NSCoder)` valeur si le type est conforme à. `NSCoding` De même, ces types disposent `Encode(NSCoder)` désormais d’une méthode qui est conforme `INSCoding` à l’interface.

Impact faible: Dans la plupart des cas, cette modification n’affecte pas les applications, car les constructeurs anciens et supprimés n’ont pas pu être utilisés.

## <a name="further-tips"></a>Autres conseils

Les modifications supplémentaires à connaître sont répertoriées dans les [conseils pour la mise à jour des applications vers le API unifiée](~/cross-platform/macios/unified/updating-tips.md).

## <a name="sample-code"></a>Exemple de code

Depuis le 31 juillet, nous avons publié des ports des exemples iOS sur cette nouvelle API sur la `magic-types` succursale, à l’adresse Hypertouch [-Samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

Pour Mac, nous vérifions des exemples dans le référentiel [Mac-Samples](https://github.com/xamarin/mac-samples) (qui montre les nouvelles API dans Mavericks/Yosemite) ainsi que des exemples de 32/64 bits dans la branche de types magiques [Mac-Samples](https://github.com/xamarin/monotouch-samples/commits/magic-types).

## <a name="related-links"></a>Liens associés

- [Mise à jour des applications iOS](updating-ios-apps.md)
- [Mise à jour des applications Mac](updating-mac-apps.md)
- [Mise à jour des applications Xamarin. Forms](updating-xamarin-forms-apps.md)
- [Mise à jour des liaisons](update-binding.md)
- [Mise à jour des conseils](updating-tips.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
