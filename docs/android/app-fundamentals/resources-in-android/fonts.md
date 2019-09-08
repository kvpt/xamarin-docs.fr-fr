---
title: Polices
ms.prod: xamarin
ms.assetid: 3F543FC5-FDED-47F8-8D2C-481FCC98BFDA
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 09/09/2018
ms.openlocfilehash: 4d91ba7b71f2eb61d003700269675c785cbfb0c8
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70755041"
---
# <a name="fonts"></a>Polices

## <a name="overview"></a>Présentation

À partir du niveau d’API 26, le Android SDK permet de traiter les polices comme des ressources, tout comme une disposition ou un drawables. La [bibliothèque de prise en charge Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/26.1.0.1) rétroporter les nouvelles API de police pour les applications qui ciblent le niveau d’API 14 ou supérieur.

Après avoir ciblé l’API 26 ou installé la bibliothèque de prise en charge Android V26, il existe deux façons d’utiliser des polices dans une application Android :

1. **Empaqueter la police en tant que ressource Android** &ndash; cela garantit que la police est toujours disponible pour l’application, mais augmente la taille du apk.
2. **Télécharger les polices** Android prend également en charge le téléchargement d’une police à partir d’un _fournisseur de polices._ &ndash; Le fournisseur de polices vérifie si la police est déjà sur l’appareil. Si nécessaire, la police est téléchargée et mise en cache sur l’appareil. Cette police peut être partagée entre plusieurs applications.

Des polices similaires (ou une police qui peut avoir plusieurs styles différents) peuvent être regroupées dans des _familles de polices_. Cela permet aux développeurs de spécifier certains attributs de la police, tels que son poids, et Android sélectionne automatiquement la police appropriée dans la famille de polices.

La bibliothèque de prise en charge Android V26 prendra en charge rétroporter pour les polices au niveau d’API 26. Lorsque vous ciblez les anciens niveaux d’API, il est nécessaire `app` de déclarer l’espace de noms XML et de nommer les `android:` différents attributs de `app:` police à l’aide de l’espace de noms et de l’espace de noms. Si seul l' `android:` espace de noms est utilisé, les polices ne s’affichent pas pour les appareils exécutant le niveau d’API 25 ou moins. Par exemple, cet extrait de code XML déclare une nouvelle ressource de [_famille de polices_](#font_families) qui fonctionnera au niveau de l’API 14 et versions ultérieures :

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family
    xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto">

     <font  android:font="@font/sourcesanspro_regular"
            android:fontStyle="normal"
            android:fontWeight="400"
            app:font="@font/sourcesanspro_regular"
            app:fontStyle="normal"
            app:fontWeight="400" />

</font-family>
```

Tant que les polices sont fournies à une application Android de manière appropriée, elles peuvent être appliquées à un widget d’interface utilisateur en définissant l' [ `fontFamily` attribut](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily). Par exemple, l’extrait de code suivant montre comment afficher une police dans un TextView :

```xml
<TextView
    android:text="The quick brown fox jumped over the lazy dog."
    android:fontFamily="@font/sourcesanspro_regular"
    app:fontFamily="@font/sourcesanspro_regular"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:layout_width="match_parent"
    android:layout_height="wrap_content" />
```

Ce guide aborde en premier le mode d’utilisation des polices comme ressource Android, puis vous explique comment télécharger des polices au moment de l’exécution.

## <a name="fonts-as-a-resource"></a>Polices en tant que ressource

L’empaquetage d’une police dans un APK Android garantit qu’elle est toujours disponible pour l’application. Un fichier de police (. TTF ou. OTF) est ajouté à une application Xamarin. Android comme n’importe quelle autre ressource, en copiant les fichiers dans un sous-répertoire du dossier **Resources** d’un projet Xamarin. Android. Les ressources de polices sont conservées dans un sous-répertoire de **police** du dossier **ressources** du projet.

> [!NOTE]
> Les polices doivent avoir une **action** de génération **AndroidResource** ou ne pas être empaquetées dans le apk final. L’action de génération doit être définie automatiquement par l’IDE.

Lorsqu’il existe de nombreux fichiers de polices similaires (par exemple, la même police avec des pondérations ou des styles différents), il est possible de les regrouper dans une famille de polices.

<a name="font_families" />

### <a name="font-families"></a>Familles de polices

Une famille de polices est un ensemble de polices qui ont des pondérations et des styles différents. Par exemple, il peut y avoir des fichiers de polices distincts pour les polices en gras ou en italique. La famille de polices est définie `font` par les éléments d’un fichier XML qui est conservé dans le répertoire **Resources/font** . Chaque famille de polices doit avoir son propre fichier XML.

Pour créer une famille de polices, commencez par ajouter toutes les polices au dossier **Resources/font** . Créez ensuite un nouveau fichier XML dans le dossier de polices pour la famille de polices. Le nom du fichier XML n’a aucune affinité ou relation avec les polices référencées ; le fichier de ressources peut être n’importe quel nom de fichier de ressources Android légal. Ce fichier XML aura un élément racine `font-family` qui contient un ou plusieurs `font` éléments. Chaque `font` élément déclare les attributs d’une police.

Le code XML suivant est un exemple de famille de polices pour la police _San Pro_ qui définit de nombreux poids de police différents. Celui-ci est enregistré en tant que fichier dans le dossier **Resources/font** nommé **sourcesanspro. xml**:

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto">
    <font android:font="@font/sourcesanspro_regular"
          android:fontStyle="normal"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_regular"
          app:fontStyle="normal"
          app:fontWeight="400" />
    <font android:font="@font/sourcesanspro_bold"
          android:fontStyle="normal"
          android:fontWeight="800"
          app:font="@font/sourcesanspro_bold"
          app:fontStyle="normal"
          app:fontWeight="800" />
    <font android:font="@font/sourcesanspro_italic"
          android:fontStyle="italic"
          android:fontWeight="400"
          app:font="@font/sourcesanspro_italic"
          app:fontStyle="italic"
          app:fontWeight="400" />
</font-family>
```

L' `fontStyle` attribut a deux valeurs possibles :

- **normal** &ndash; une police normale
- **italique** &ndash; une police en italique

L' `fontWeight` attribut correspond à l’attribut `font-weight` CSS et fait référence à l’épaisseur de la police. Il s’agit d’une valeur comprise dans la plage 100-900. La liste suivante décrit les valeurs de poids de police courantes et leur nom :

- **Fin** &ndash; 100
- **Très clair** &ndash; 200
- **Clair** &ndash; 300
- **Normal** &ndash; 400
- **Moyenne** &ndash; 500
- **Semi-gras** &ndash; 600
- **Gras** &ndash; 700
- **Extra-gras** &ndash; 800
- **Noir** &ndash; 900

Une fois qu’une famille de polices a été définie, elle peut être utilisée de `fontFamily`façon `textStyle`déclarative en `fontWeight` définissant les attributs, et dans le fichier de disposition.  Par exemple, l’extrait de code XML suivant définit une police de pondération 600 (normale) et un style de texte en italique :

```xml
<TextView
    android:text="Sans Source Pro semi-bold italic, 600 weight, italic"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"
    android:fontFamily="@font/sourcesanspro"
    android:textAppearance="?android:attr/textAppearanceLarge"
    android:gravity="center_horizontal"
    android:fontWeight="600"
    android:textStyle="italic"
    />
```

### <a name="programmatically-assigning-fonts"></a>Affecter des polices par programmation

Les polices peuvent être définies par programmation à l’aide [`Resources.GetFont`](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int)) de la méthode pour [`Typeface`](https://developer.android.com/reference/android/graphics/Typeface.html) récupérer un objet. De nombreux affichages `TypeFace` ont une propriété qui peut être utilisée pour assigner la police au widget. Cet extrait de code montre comment définir par programmation la police sur un TextView :

```csharp
Android.Graphics.Typeface typeface = this.Resources.GetFont(Resource.Font.caveat_regular);
textView1.Typeface = typeface;
textView1.Text = "Changed the font";
```

La `GetFont` méthode charge automatiquement la première police dans une famille de polices.  Pour charger une police qui correspond à un style spécifique, utilisez `Typeface.Create` la méthode. Cette méthode essaiera de charger une police qui correspond au style spécifié. Par exemple, cet extrait de code essaiera de charger un objet `Typeface` en gras à partir d’une famille de polices définie dans **ressources/polices**:

```csharp
var typeface = Typeface.Create("<FONT FAMILY NAME>", Android.Graphics.TypefaceStyle.Bold);
textView1.Typeface = typeface;
```

## <a name="downloading-fonts"></a>Téléchargement des polices

Au lieu d’empaqueter des polices en tant que ressource d’application, Android peut télécharger des polices à partir d’une source distante. Cela aura l’effet souhaitable de réduire la taille du APK.

Les polices sont téléchargées avec l’assistance d’un _fournisseur de polices_. Il s’agit d’un fournisseur de contenu spécialisé qui gère le téléchargement et la mise en cache des polices dans toutes les applications sur l’appareil. Android 8,0 comprend un fournisseur de polices pour télécharger des polices à partir du [référentiel de polices Google](http://fonts.google.com). Ce fournisseur de polices par défaut est reporté au niveau d’API 14 avec la bibliothèque de prise en charge Android V26.

Quand une application effectue une requête pour une police, le fournisseur de polices vérifie d’abord si la police est déjà sur l’appareil. Si ce n’est pas le cas, il tentera de télécharger la police. Si la police ne peut pas être téléchargée, Android utilise la police système par défaut. Une fois la police téléchargée, elle est disponible pour toutes les applications sur l’appareil, et pas seulement pour l’application qui a effectué la demande initiale.

Lorsqu’une demande est effectuée pour télécharger une police, l’application n’interroge pas directement le fournisseur de polices. Au lieu de cela, les applications utiliseront [`FontsContract`](https://developer.android.com/reference/android/provider/FontsContract.html) une instance de l' [`FontsContractCompat`](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html) API (ou si la bibliothèque de prise en charge 26 est utilisée).  

Android 8,0 prend en charge le téléchargement des polices de deux manières différentes :

1. **Déclarer des polices téléchargeables en tant que ressource** &ndash; Une application peut déclarer des polices téléchargeables sur Android via des fichiers de ressources XML. Ces fichiers contiennent toutes les métadonnées dont Android a besoin pour télécharger de manière asynchrone les polices lorsque l’application démarre et les met en cache sur l’appareil.
2. **Par programmation** &ndash; Les API dans le niveau d’API Android 26 permettent à une application de télécharger les polices par programme, pendant que l’application est en cours d’exécution. Les applications créent un `FontRequest` objet pour une police donnée et passent cet objet à la `FontsContract` classe. Prend le `FontRequest` et récupère la police d’un fournisseur de _polices._ `FontsContract` Android télécharge la police en mode synchrone. Un exemple de création d' `FontRequest` un sera présenté plus loin dans ce guide.

Quelle que soit l’approche utilisée, les fichiers de ressources qui doivent être ajoutés à l’application Xamarin. Android avant le téléchargement des polices. Tout d’abord, la ou les polices doivent être déclarées dans un fichier XML dans le répertoire **Resources/font** dans le cadre d’une famille de polices. Cet extrait de code est un exemple de téléchargement de polices à partir du [regroupement Open source Google polices](https://fonts.google.com) à l’aide du fournisseur de polices par défaut fourni avec Android 8,0 (ou la bibliothèque de prise en charge V26) :

```xml
<?xml version="1.0" encoding="utf-8"?>
<font-family xmlns:android="http://schemas.android.com/apk/res/android"
             xmlns:app="http://schemas.android.com/apk/res-auto"
             android:fontProviderAuthority="com.google.android.gms.fonts"
             android:fontProviderPackage="com.google.android.gms"
             android:fontProviderQuery="VT323"
             android:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
             app:fontProviderAuthority="com.google.android.gms.fonts"
             app:fontProviderPackage="com.google.android.gms"
             app:fontProviderQuery="VT323"
             app:fontProviderCerts="@array/com_google_android_gms_fonts_certs"
>
</font-family>
```

L' `font-family` élément contient les attributs suivants, qui déclarent les informations requises par Android pour télécharger les polices :

1. **fontProviderAuthority** &ndash; Autorité du fournisseur de polices à utiliser pour la requête.
2. **fontPackage** &ndash; Package pour le fournisseur de polices à utiliser pour la requête. Cela permet de vérifier l’identité du fournisseur.
3. **fontQuery** &ndash; Il s’agit d’une chaîne qui aide le fournisseur de polices à localiser la police demandée. Les détails sur la requête de police sont spécifiques au fournisseur de polices. La [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe de l’exemple d’application de [polices téléchargeables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fournit des informations sur le format de requête pour les polices de la collection Open source Google Fonts.
4. **fontProviderCerts** &ndash; Tableau de ressources avec la liste des jeux de hachages pour les certificats avec lesquels le fournisseur doit être signé.

Une fois les polices définies, il peut être nécessaire de fournir des informations sur les _certificats de police_ impliqués dans le téléchargement.

### <a name="font-certificates"></a>Certificats de police

Si le fournisseur de polices n’est pas préinstallé sur l’appareil, ou si l’application utilise la `Xamarin.Android.Support.Compat` bibliothèque, Android requiert les certificats de sécurité du fournisseur de polices. Ces certificats sont listés dans un fichier de ressources de tableau qui est conservé dans le répertoire des **ressources/valeurs** .

Par exemple, le code XML suivant est nommé **Resources/values/fonts_cert. xml** et stocke les certificats pour le fournisseur de polices Google :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="com_google_android_gms_fonts_certs">
        <item>@array/com_google_android_gms_fonts_certs_dev</item>
        <item>@array/com_google_android_gms_fonts_certs_prod</item>
    </array>
    <string-array name="com_google_android_gms_fonts_certs_dev">
        <item>
            MIIEqDCCA5CgAwIBAgIJANWFuGx90071MA0GCSqGSIb3DQEBBAUAMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTAeFw0wODA0MTUyMzM2NTZaFw0zNTA5MDEyMzM2NTZaMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbTCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBANbOLggKv+IxTdGNs8/TGFy0PTP6DHThvbbR24kT9ixcOd9W+EaBPWW+wPPKQmsHxajtWjmQwWfna8mZuSeJS48LIgAZlKkpFeVyxW0qMBujb8X8ETrWy550NaFtI6t9+u7hZeTfHwqNvacKhp1RbE6dBRGWynwMVX8XW8N1+UjFaq6GCJukT4qmpN2afb8sCjUigq0GuMwYXrFVee74bQgLHWGJwPmvmLHC69EH6kWr22ijx4OKXlSIx2xT1AsSHee70w5iDBiK4aph27yH3TxkXy9V89TDdexAcKk/cVHYNnDBapcavl7y0RiQ4biu8ymM8Ga/nmzhRKya6G0cGw8CAQOjgfwwgfkwHQYDVR0OBBYEFI0cxb6VTEM8YYY6FbBMvAPyT+CyMIHJBgNVHSMEgcEwgb6AFI0cxb6VTEM8YYY6FbBMvAPyT+CyoYGapIGXMIGUMQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEQMA4GA1UEChMHQW5kcm9pZDEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDEiMCAGCSqGSIb3DQEJARYTYW5kcm9pZEBhbmRyb2lkLmNvbYIJANWFuGx90071MAwGA1UdEwQFMAMBAf8wDQYJKoZIhvcNAQEEBQADggEBABnTDPEF+3iSP0wNfdIjIz1AlnrPzgAIHVvXxunW7SBrDhEglQZBbKJEk5kT0mtKoOD1JMrSu1xuTKEBahWRbqHsXclaXjoBADb0kkjVEJu/Lh5hgYZnOjvlba8Ld7HCKePCVePoTJBdI4fvugnL8TsgK05aIskyY0hKI9L8KfqfGTl1lzOv2KoWD0KWwtAWPoGChZxmQ+nBli+gwYMzM1vAkP+aayLe0a1EQimlOalO762r0GXO0ks+UeXde2Z4e+8S/pf7pITEI/tP+MxJTALw9QUWEv9lKTk+jkbqxbsh8nfBUapfKqYn0eidpwq2AzVp3juYl7//fKnaPhJD9gs=
        </item>
    </string-array>
    <string-array name="com_google_android_gms_fonts_certs_prod">
        <item>
            MIIEQzCCAyugAwIBAgIJAMLgh0ZkSjCNMA0GCSqGSIb3DQEBBAUAMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDAeFw0wODA4MjEyMzEzMzRaFw0zNjAxMDcyMzEzMzRaMHQxCzAJBgNVBAYTAlVTMRMwEQYDVQQIEwpDYWxpZm9ybmlhMRYwFAYDVQQHEw1Nb3VudGFpbiBWaWV3MRQwEgYDVQQKEwtHb29nbGUgSW5jLjEQMA4GA1UECxMHQW5kcm9pZDEQMA4GA1UEAxMHQW5kcm9pZDCCASAwDQYJKoZIhvcNAQEBBQADggENADCCAQgCggEBAKtWLgDYO6IIrgqWbxJOKdoR8qtW0I9Y4sypEwPpt1TTcvZApxsdyxMJZ2JORland2qSGT2y5b+3JKkedxiLDmpHpDsz2WCbdxgxRczfey5YZnTJ4VZbH0xqWVW/8lGmPav5xVwnIiJS6HXk+BVKZF+JcWjAsb/GEuq/eFdpuzSqeYTcfi6idkyugwfYwXFU1+5fZKUaRKYCwkkFQVfcAs1fXA5V+++FGfvjJ/CxURaSxaBvGdGDhfXE28LWuT9ozCl5xw4Yq5OGazvV24mZVSoOO0yZ31j7kYvtwYK6NeADwbSxDdJEqO4k//0zOHKrUiGYXtqw/A0LFFtqoZKFjnkCAQOjgdkwgdYwHQYDVR0OBBYEFMd9jMIhF1Ylmn/Tgt9r45jk14alMIGmBgNVHSMEgZ4wgZuAFMd9jMIhF1Ylmn/Tgt9r45jk14aloXikdjB0MQswCQYDVQQGEwJVUzETMBEGA1UECBMKQ2FsaWZvcm5pYTEWMBQGA1UEBxMNTW91bnRhaW4gVmlldzEUMBIGA1UEChMLR29vZ2xlIEluYy4xEDAOBgNVBAsTB0FuZHJvaWQxEDAOBgNVBAMTB0FuZHJvaWSCCQDC4IdGZEowjTAMBgNVHRMEBTADAQH/MA0GCSqGSIb3DQEBBAUAA4IBAQBt0lLO74UwLDYKqs6Tm8/yzKkEu116FmH4rkaymUIE0P9KaMftGlMexFlaYjzmB2OxZyl6euNXEsQH8gjwyxCUKRJNexBiGcCEyj6z+a1fuHHvkiaai+KL8W1EyNmgjmyy8AW7P+LLlkR+ho5zEHatRbM/YAnqGcFh5iZBqpknHf1SKMXFh4dd239FJ1jWYfbMDMy3NS5CTMQ2XFI1MvcyUTdZPErjQfTbQe3aDQsQcafEQPD+nqActifKZ0Np0IS9L9kR/wbNvyz6ENwPiTrjV2KRkEjH78ZMcUQXg0L3BYHJ3lc69Vs5Ddf9uUGGMYldX3WfMBEmh/9iFBDAaTCK
        </item>
    </string-array>
</resources>
```

Lorsque ces fichiers de ressources sont en place, l’application peut télécharger les polices.

### <a name="declaring-downloadable-fonts-as-resources"></a>Déclaration des polices téléchargeables en tant que ressources

En répertoriant les polices téléchargeables dans **fichier AndroidManifest. xml**, Android télécharge les polices de manière asynchrone lors du premier démarrage de l’application. Les polices elles-mêmes sont répertoriées dans un fichier de ressources de tableau, comme celle-ci :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <array name="downloadable_fonts" translatable="false">
        <item>@font/vt323</item>
    </array>
</resources>
```

Pour télécharger ces polices, elles doivent être déclarées dans **fichier AndroidManifest. xml** en ajoutant `meta-data` en tant qu’enfant de `application` l’élément. Par exemple, si les polices téléchargeables sont déclarées dans un fichier de ressources au niveau des **ressources/valeurs/downloadable_fonts. xml**, cet extrait de code doit être ajouté au manifeste :

```xml
<meta-data android:name="downloadable_fonts" android:resource="@array/downloadable_fonts" />
```

### <a name="downloading-a-font-with-the-font-apis"></a>Téléchargement d’une police avec les API de police

Il est possible de télécharger une police par programmation en instanciant un [`FontRequest`](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html) objet et en le passant à `FontContractCompat.RequestFont` la méthode. La `FontContractCompat.RequestFont` méthode vérifie d’abord si la police existe sur l’appareil, puis, si nécessaire, interroge de manière asynchrone le fournisseur de polices et tente de télécharger la police de l’application. Si `FontRequest` le n’est pas en mesure de télécharger la police, Android utilise la police système par défaut.

Un `FontRequest` objet contient des informations qui seront utilisées par le fournisseur de polices pour rechercher et télécharger une police. Un `FontRequest` requiert quatre éléments d’information :

1. **Autorité du fournisseur de polices** &ndash; Autorité du fournisseur de polices à utiliser pour la requête.
2. **Package de polices** &ndash; Package pour le fournisseur de polices à utiliser pour la requête. Cela permet de vérifier l’identité du fournisseur.
3. **Requête de police** &ndash; Il s’agit d’une chaîne qui aide le fournisseur de polices à localiser la police demandée. Les détails sur la requête de police sont spécifiques au fournisseur de polices. Les détails de la chaîne sont spécifiques au fournisseur de polices. La [`QueryBuilder`](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/DownloadableFonts/QueryBuilder.cs) classe de l’exemple d’application de [polices téléchargeables](https://github.com/xamarin/monodroid-samples/blob/master/android-o/DownloadableFonts/) fournit des informations sur le format de requête pour les polices de la collection Open source Google Fonts.
4. **Certificats du fournisseur de polices** &ndash; Tableau de ressources avec la liste des jeux de hachages pour les certificats avec lesquels le fournisseur doit être signé.

Cet extrait de code est un exemple d’instanciation d’un nouvel `FontRequest` objet :

```csharp
FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms", <FontToDownload>, Resource.Array.com_google_android_gms_fonts_certs);
```

Dans l’extrait `FontToDownload` de code précédent se trouve une requête qui permet d’obtenir la police de la collection Open source Google polices.

Avant `FontRequest` de passer à la `FontContractCompat.RequestFont` méthode, il existe deux objets qui doivent être créés :

- **`FontsContractCompat.FontRequestCallback`** &ndash; Il s’agit d’une classe abstraite qui doit être étendue. Il s’agit d’un rappel qui sera appelé lorsque `RequestFont` est terminé. Une application Xamarin. Android doit effectuer une `FontsContractCompat.FontRequestCallback` sous-classe et `OnTypefaceRequestFailed` remplacer `OnTypefaceRetrieved`la et, en fournissant les actions à entreprendre lorsque le téléchargement échoue ou réussit respectivement.
- **`Handler`** Il s’agit `Handler` d’un qui sera utilisé `RequestFont` par pour télécharger la police sur un thread, si nécessaire. &ndash; Les polices ne doivent **pas** être téléchargées sur le thread d’interface utilisateur.

Cet extrait de code est un exemple C# de classe qui télécharge de manière asynchrone une police de la collection Open source Google polices. Il implémente l' `FontRequestCallback` interface et déclenche un C# événement lorsque `FontRequest` est terminé.

```csharp
public class FontDownloadHelper : FontsContractCompat.FontRequestCallback
{
    // A very simple font query; replace as necessary
    public static readonly String FontToDownload = "Courgette";

    Android.OS.Handler Handler = null;

    public event EventHandler<FontDownloadEventArg> FontDownloaded = delegate
    {
        // just an empty delegate to avoid null reference exceptions.  
    };

    public void DownloadFonts(Context context)
    {
        FontRequest request = new FontRequest("com.google.android.gms.fonts", "com.google.android.gms",FontToDownload , Resource.Array.com_google_android_gms_fonts_certs);
        FontsContractCompat.RequestFont(context, request, this, GetHandlerThreadHandler());
    }

    public override void OnTypefaceRequestFailed(int reason)
    {
        base.OnTypefaceRequestFailed(reason);
        FontDownloaded(this, new FontDownloadEventArg(null));
    }

    public override void OnTypefaceRetrieved(Android.Graphics.Typeface typeface)
    {
        base.OnTypefaceRetrieved(typeface);
        FontDownloaded(this, new FontDownloadEventArg(typeface));
    }

    Handler GetHandlerThreadHandler()
    {
        if (Handler == null)
        {
            HandlerThread handlerThread = new HandlerThread("fonts");
            handlerThread.Start();
            Handler = new Handler(handlerThread.Looper);
        }
        return Handler;
    }
}

public class FontDownloadEventArg : EventArgs
{
    public FontDownloadEventArg(Android.Graphics.Typeface typeface)
    {
        Typeface = typeface;
    }
    public Android.Graphics.Typeface Typeface { get; private set; }
    public bool RequestFailed
    {
        get
        {
            return Typeface != null;
        }
    }
}
```

Pour utiliser cette application d’assistance, une `FontDownloadHelper` nouvelle est créée et un gestionnaire d’événements est affecté :  

```csharp
var fontHelper = new FontDownloadHelper();

fontHelper.FontDownloaded += (object sender, FontDownloadEventArg e) =>
{
    //React to the request
};
fontHelper.DownloadFonts(this); // this is an Android Context instance.
```

## <a name="summary"></a>Récapitulatif

Ce guide a présenté les nouvelles API d’Android 8,0 pour prendre en charge les polices et les polices téléchargeables en tant que ressources. Il a expliqué comment incorporer des polices existantes dans un APK et comment les utiliser dans une disposition. Elle a également expliqué comment Android 8,0 prend en charge le téléchargement des polices d’un fournisseur de polices, par programmation ou en déclarant les métadonnées de police dans les fichiers de ressources.

## <a name="related-links"></a>Liens associés

- [fontFamily](https://developer.android.com/reference/android/widget/TextView.html#attr_android:fontFamily)
- [FontConfig](https://developer.android.com/reference/android/text/FontConfig.html)
- [FontRequest](https://developer.android.com/reference/android/support/v4/provider/FontRequest.html)
- [FontsContractCompat](https://developer.android.com/reference/android/support/v4/provider/FontsContractCompat.html)
- [Resources.GetFont](https://developer.android.com/reference/android/content/res/Resources.html#getFont(int))
- [Typeface](https://developer.android.com/reference/android/graphics/Typeface.html)
- [Bibliothèque de prise en charge Android 26 NuGet](https://www.nuget.org/packages/Xamarin.Android.Support.Compat/)
- [Utilisation des polices dans Android](https://www.youtube.com/watch?v=TfB-TsLFJdM)
- [Spécification de l’épaisseur de police CSS](https://www.w3.org/TR/css-fonts-3/#font-weight-numeric-values)
- [Collection Open source Google polices](https://fonts.google.com/)
- [Source sans Pro](https://fonts.google.com/specimen/Source+Sans+Pro)
