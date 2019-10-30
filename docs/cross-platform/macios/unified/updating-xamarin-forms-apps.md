---
title: Mise à jour des applications Xamarin. Forms existantes
description: Ce document décrit les étapes à suivre pour mettre à jour une application Xamarin. Forms à partir du API classique vers le API unifiée.
ms.prod: xamarin
ms.assetid: C2F0D1D1-256D-44A4-AAC9-B06A0CB41E70
author: davidortinau
ms.author: daortin
ms.date: 03/29/2017
ms.openlocfilehash: dad1b7173e302931455887fdaa4730347f0e5e55
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73015003"
---
# <a name="updating-existing-xamarinforms-apps"></a>Mise à jour des applications Xamarin. Forms existantes

_Procédez comme suit pour mettre à jour une application Xamarin. Forms existante afin d’utiliser la API unifiée et de la mettre à jour vers la version 1.3.1_

> [!IMPORTANT]
> Comme Xamarin. Forms 1.3.1 est la première version qui prend en charge le API unifiée, la solution complète doit être mise à jour pour utiliser la dernière version en même temps que la migration de l’application iOS vers Unified. Cela signifie qu’en plus de mettre à jour le projet iOS pour la prise en charge unifiée, vous devez également modifier le code dans _tous_ les projets de la solution.

La mise à jour est effectuée en deux étapes :

1. Migrez l’application iOS vers le API unifiée à l’aide de l’outil de migration de Visual Studio pour Mac de la Build.

    - Utilisez l’outil de migration pour mettre à jour automatiquement le projet.

    - Mettez à jour les API natives iOS comme indiqué dans les instructions pour [mettre à jour les applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md) (en particulier dans le code de rendu personnalisé ou de service de dépendances).

2. Mettez à jour la totalité de la solution vers Xamarin. Forms version 1,3.

    1. Installez le package NuGet Xamarin. Forms 1.3.1.

    2. Mettez à jour la classe `App` dans le code partagé.

    3. Mettez à jour le `AppDelegate` dans le projet iOS.

    4. Mettez à jour le `MainActivity` dans le projet Android.

    5. Mettez à jour le `MainPage` dans le projet Windows Phone.

## <a name="1-ios-app-unified-migration"></a>1. application iOS (migration unifiée)

Une partie de la migration nécessite la mise à niveau de Xamarin. Forms vers la version 1,3, qui prend en charge le API unifiée. Pour que les références d’assembly appropriées soient créées, nous devons tout d’abord mettre à jour le projet iOS pour utiliser le API unifiée.

### <a name="migration-tool"></a>Outil de migration

Cliquez sur le projet iOS pour le sélectionner, puis choisissez **projet > migrer vers Xamarin. iOS API unifiée...** et acceptez le message d’avertissement qui s’affiche.

![](updating-xamarin-forms-apps-images/beta-tool1.png "Choose Project > Migrate to Xamarin.iOS Unified API... and agree to the warning message that appears")

Cela va automatiquement :

- Modifiez le type de projet pour prendre en charge l’API unifiée 64 bits.
- Modifiez la référence de Framework à **Xamarin. iOS** (en remplaçant **l’ancienne référence** monopoint).
- Modifiez les références d’espace de noms dans le code pour supprimer le préfixe `MonoTouch`.
- Mettez à jour le fichier **csproj** pour utiliser les cibles de génération appropriées pour le API unifiée.

**Nettoyez** et **Générez** le projet pour vérifier qu’il n’y a pas d’autres erreurs à résoudre. Aucune action supplémentaire ne doit être nécessaire. Ces étapes sont expliquées plus en détail dans la [documentation API unifiée](~/cross-platform/macios/unified/updating-ios-apps.md).

### <a name="update-native-ios-apis-if-required"></a>Mettre à jour les API iOS natives (si nécessaire)

Si vous avez ajouté du code natif iOS supplémentaire (par exemple, des convertisseurs personnalisés ou des services de dépendance), vous devrez peut-être effectuer des corrections de code manuel supplémentaires. Recompilez votre application et reportez-vous aux instructions relatives à la [mise à jour des applications iOS existantes](~/cross-platform/macios/unified/updating-ios-apps.md) pour plus d’informations sur les modifications qui peuvent être nécessaires. [Ces conseils](~/cross-platform/macios/unified/updating-tips.md) vous aideront également à identifier les modifications requises.

## <a name="2-xamarinforms-131-update"></a>2. Xamarin. Forms 1.3.1 Update

Une fois que l’application iOS a été mise à jour vers le API unifiée, le reste de la solution doit être mis à jour vers Xamarin. Forms version 1.3.1. Cela concerne :

- Mise à jour du package NuGet Xamarin. Forms dans chaque projet.
- Modification du code pour utiliser les nouvelles classes Xamarin. Forms `Application`, `FormsApplicationDelegate` (iOS), `FormsApplicationActivity` (Android) et `FormsApplicationPage` (Windows Phone).

Ces étapes sont expliquées ci-dessous :

### <a name="21-update-nuget-in-all-projects"></a>2,1 mettre à jour NuGet dans tous les projets

Mettez à jour Xamarin. Forms à la version 1.3.1 en préversion à l’aide du gestionnaire de package NuGet pour tous les projets de la solution : PCL (le cas échéant), iOS, Android et Windows Phone. Il est recommandé de **supprimer et de rajouter** le package NuGet Xamarin. Forms pour effectuer la mise à jour vers la version 1,3.

> [!NOTE]
> Xamarin. Forms version 1.3.1 est actuellement en version *préliminaire*. Cela signifie que vous devez sélectionner **l’option de** préversion dans NuGet (à l’aide d’une case à cocher dans Visual Studio pour Mac ou une liste déroulante dans Visual Studio) pour afficher la version préliminaire la plus récente.

> [!IMPORTANT]
> Si vous utilisez Visual Studio, vérifiez que la dernière version du gestionnaire de package NuGet est installée. Les versions antérieures de NuGet dans Visual Studio n’installeront pas correctement la version unifiée de Xamarin. Forms 1.3.1. Accédez à **outils > extensions et mises à jour...** , puis cliquez sur la liste **installé** pour vérifier que le **Gestionnaire de package NuGet pour Visual Studio** est au moins la version 2.8.5. Si elle est plus ancienne, cliquez sur la liste des **mises à jour** pour télécharger la dernière version.

Une fois que vous avez mis à jour le package NuGet vers Xamarin. Forms 1.3.1, apportez les modifications suivantes dans chaque projet pour effectuer une mise à niveau vers la nouvelle classe `Xamarin.Forms.Application`.

### <a name="22-portable-class-library-or-shared-project"></a>Bibliothèque de classes portable 2,2 (ou projet partagé)

Modifiez le fichier **app.cs** de façon à ce que :

- La classe `App` hérite désormais de `Application`.
- La propriété `MainPage` est définie sur la première page de contenu que vous souhaitez afficher.

```csharp
public class App : Application // superclass new in 1.3
{
    public App ()
    {
        // The root page of your application
        MainPage = new ContentPage {...}; // property new in 1.3
    }
```

Nous avons complètement supprimé la méthode `GetMainPage` et défini à la place la *propriété* `MainPage` sur la sous-classe `Application`.

Cette nouvelle classe de base `Application` prend également en charge les remplacements `OnStart`, `OnSleep`et `OnResume` pour vous aider à gérer le cycle de vie de votre application.

La classe `App` est ensuite transmise à une nouvelle méthode `LoadApplication` dans chaque projet d’application, comme décrit ci-dessous :

### <a name="23-ios-app"></a>Application 2,3 iOS

Modifiez le fichier **AppDelegate.cs** de façon à ce que :

- La classe hérite de `FormsApplicationDelegate` (au lieu de `UIApplicationDelegate` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de `App`.

```csharp
[Register ("AppDelegate")]
public partial class AppDelegate :
    global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate // superclass new in 1.3
{
    public override bool FinishedLaunching (UIApplication app, NSDictionary options)
    {
        global::Xamarin.Forms.Forms.Init ();

        LoadApplication (new App ());  // method is new in 1.3

        return base.FinishedLaunching (app, options);
    }
}
```

### <a name="23-android-app"></a>Application Android 2,3

Modifiez le fichier **MainActivity.cs** de façon à ce que :

- La classe hérite de `FormsApplicationActivity` (au lieu de `FormsActivity` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de `App`

```csharp
[Activity (Label = "YOURAPPNAM", Icon = "@drawable/icon", MainLauncher = true,
ConfigurationChanges = ConfigChanges.ScreenSize | ConfigChanges.Orientation)]
public class MainActivity :
    global::Xamarin.Forms.Platform.Android.FormsApplicationActivity // superclass new in 1.3
{
    protected override void OnCreate (Bundle bundle)
    {
        base.OnCreate (bundle);

        global::Xamarin.Forms.Forms.Init (this, bundle);

        LoadApplication (new App ()); // method is new in 1.3
    }
}
```

### <a name="24-windows-phone-app"></a>Application Windows Phone 2,4

Nous devons mettre à jour la **MainPage** , à la fois le XAML et le codebehind.

Modifiez le fichier **MainPage. Xaml** afin que :

- L’élément XAML racine doit être `winPhone:FormsApplicationPage`.
- L’attribut `xmlns:phone` doit être *remplacé* par `xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"`

Un exemple mis à jour est illustré ci-dessous : vous devez uniquement modifier ces éléments (le reste des attributs doit rester identique) :

```xml
<winPhone:FormsApplicationPage
   ...
   xmlns:winPhone="clr-namespace:Xamarin.Forms.Platform.WinPhone;assembly=Xamarin.Forms.Platform.WP8"
    ...>
</winPhone:FormsApplicationPage>
```

Modifiez le fichier **MainPage.Xaml.cs** de façon à ce que :

- La classe hérite de `FormsApplicationPage` (au lieu de `PhoneApplicationPage` précédemment).
- `LoadApplication` est appelé avec une nouvelle instance de la classe Xamarin. Forms `App`. Vous devrez peut-être qualifier entièrement cette référence, car Windows Phone possède sa propre classe `App` déjà définie.

```csharp
public partial class MainPage : global::Xamarin.Forms.Platform.WinPhone.FormsApplicationPage // superclass new in 1.3
{
    public MainPage()
    {
        InitializeComponent();
        SupportedOrientations = SupportedPageOrientation.PortraitOrLandscape;

        global::Xamarin.Forms.Forms.Init();
        LoadApplication(new YOUR_APP_NAMESPACE.App()); // new in 1.3
    }
 }
```

### <a name="troubleshooting"></a>Résolution des problèmes

Parfois, une erreur semblable à celle-ci s’affiche après la mise à jour du package NuGet Xamarin. Forms. Cela se produit lorsque le programme de mise à jour NuGet ne supprime pas complètement les références à des versions antérieures de vos fichiers **csproj** .

>VOTRE\_PROJECT. csproj : error : ce projet fait référence à un ou plusieurs packages NuGet manquants sur cet ordinateur. Activez la restauration des packages NuGet pour les télécharger.  Pour plus d'informations, consultez http://go.microsoft.com/fwlink/?LinkID=322105. Le fichier manquant est.. /.. /packages/Xamarin.Forms.1.2.3.6257/build/portable-win + Net45 + wp80 + MonoAndroid10 + MonoTouch10/Xamarin. Forms. targets. (Votre projet\_)

Pour corriger ces erreurs, ouvrez le fichier **csproj** dans un éditeur de texte et recherchez `<Target` éléments qui font référence à des versions antérieures de Xamarin. Forms, telles que l’élément indiqué ci-dessous. Vous devez supprimer manuellement cet élément entier du fichier **csproj** et enregistrer les modifications.

```csharp
  <Target Name="EnsureNuGetPackageBuildImports" BeforeTargets="PrepareForBuild">
    <PropertyGroup>
      <ErrorText>This project references NuGet package(s) that are missing on this computer. Enable NuGet Package Restore to download them.  For more information, see http://go.microsoft.com/fwlink/?LinkID=322105. The missing file is {0}.</ErrorText>
    </PropertyGroup>
    <Error Condition="!Exists('..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets')" Text="$([System.String]::Format('$(ErrorText)', '..\..\packages\Xamarin.Forms.1.2.3.6257\build\portable-win+net45+wp80+MonoAndroid10+MonoTouch10\Xamarin.Forms.targets'))" />
  </Target>
```

Le projet doit être généré correctement une fois que ces anciennes références ont été supprimées.

## <a name="considerations"></a>Éléments à prendre en considération

Les considérations suivantes doivent être prises en compte lors de la conversion d’un projet Xamarin. Forms existant de la API classique au nouveau API unifiée si cette application s’appuie sur un ou plusieurs composants ou un package NuGet.

### <a name="components"></a>Composants

Tout composant que vous avez inclus dans votre application doit également être mis à jour vers la API unifiée ou un conflit se produira lorsque vous essaierez de compiler. Pour tout composant inclus, remplacez la version actuelle par une nouvelle version du magasin de composants Xamarin qui prend en charge le API unifiée et effectuez une génération propre. Tout composant qui n’a pas encore été converti par l’auteur affiche un avertissement 32 bits uniquement dans le magasin de composants.

### <a name="nuget-support"></a>Prise en charge par NuGet

Bien que nous ayons apporté des modifications à NuGet pour qu’il fonctionne avec la prise en charge API unifiée, il n’y a pas eu de nouvelle version de NuGet. nous allons donc évaluer comment obtenir NuGet pour reconnaître les nouvelles API.

Jusqu’à ce moment-là, tout comme les composants, vous devrez basculer tout package NuGet inclus dans votre projet vers une version qui prend en charge les API unifiées et effectuer une génération propre par la suite.

> [!IMPORTANT]
> Si vous avez une erreur au format _« l’erreur 3 ne peut pas inclure à la fois «MonoTouch. dll » et « Xamarin. iOS. dll » dans le même projet Xamarin. iOS-« Xamarin. iOS. dll » est référencé explicitement, tandis que « unitouch. dll » est référencé par «xxx, version = 0.0.000, culture = neutral, PublicKeyToken = null'»_ après la conversion de votre application en API unifiées, il est généralement nécessaire de disposer d’un composant ou d’un package NuGet dans le projet qui n’a pas été mis à jour vers l’API unifiée. Vous devez supprimer le composant/NuGet existant, effectuer une mise à jour vers une version qui prend en charge les API unifiées et effectuer une génération propre.

## <a name="enabling-64-bit-builds-of-xamarinios-apps"></a>Activation des versions 64 bits des applications Xamarin. iOS

Pour une application mobile Xamarin. iOS qui a été convertie en API unifiée, le développeur doit toujours activer la génération de l’application pour les ordinateurs 64 bits à partir des options de l’application. Pour obtenir des instructions détaillées sur l’activation des versions 64 bits, consultez le document **activation des builds 64 bits des applications Xamarin. iOS** du document [sur les plateformes 32/64 bits](~/cross-platform/macios/32-and-64/index.md#enable-64) .

## <a name="summary"></a>Récapitulatif

L’application Xamarin. Forms doit maintenant être mise à jour vers la version 1.3.1 et l’application iOS migrée vers le API unifiée (qui prend en charge les architectures 64 bits sur la plateforme iOS).

Comme indiqué ci-dessus, si votre application Xamarin. Forms comprend du code natif tel que des convertisseurs personnalisés ou des services de dépendance, ceux-ci peuvent également nécessiter une mise à jour pour utiliser les nouveaux types [introduits dans le API unifiée](~/cross-platform/macios/index.md).

## <a name="related-links"></a>Liens associés

- [Mise à jour des applications iOS](~/cross-platform/macios/unified/updating-apps.md)
- [Mise à jour des applications iOS](~/cross-platform/macios/unified/updating-ios-apps.md)
- [Utilisation de types natifs dans des applications multiplateformes](~/cross-platform/macios/native-types-cross-platform.md)
- [Mise à jour des conseils](~/cross-platform/macios/unified/updating-tips.md)
- [Différences entre les API unifiée classiques et les différences](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/ios/api_changes/classic-vs-unified-8.6.0/index.md)
