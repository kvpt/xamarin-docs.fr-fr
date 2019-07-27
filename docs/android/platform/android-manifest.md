---
title: Utilisation du manifeste Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/05/2018
ms.openlocfilehash: 4a5b0e7d45878dcaa0f3e97411c2ef83d2e26c5a
ms.sourcegitcommit: b07e0259d7b30413673a793ebf4aec2b75bb9285
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/26/2019
ms.locfileid: "68510695"
---
# <a name="working-with-the-android-manifest"></a>Utilisation du manifeste Android

**Fichier AndroidManifest. xml** est un fichier puissant de la plateforme Android qui vous permet de décrire les fonctionnalités et les exigences de votre application à Android. Toutefois, il n’est pas facile de l’utiliser. Xamarin. Android permet de réduire cette difficulté en vous permettant d’ajouter des attributs personnalisés à vos classes, qui seront ensuite utilisées pour générer automatiquement le manifeste. Notre objectif est que 99% de nos utilisateurs ne doivent jamais avoir à modifier manuellement **fichier AndroidManifest. xml**. 

**Fichier AndroidManifest. xml** est généré dans le cadre du processus de génération et le code XML trouvé dans les **Propriétés/fichier AndroidManifest. xml** est fusionné avec du code XML généré à partir d’attributs personnalisés. Le **fichier AndroidManifest. xml** fusionné qui en résulte se trouve dans le sous-répertoire **obj** . par exemple, il réside dans **obj/Debug/Android/fichier AndroidManifest. xml** pour les versions Debug. Le processus de fusion est trivial: il utilise des attributs personnalisés dans le code pour générer des éléments XML et *insère* ces éléments dans **fichier AndroidManifest. xml**. 



## <a name="the-basics"></a>Principes de base

Au moment de la compilation, les assemblys sont analysés`abstract` pour rechercher les non-classes qui dérivent de l' [activité](xref:Android.App.Activity) et dont l' [`[Activity]`](xref:Android.App.ActivityAttribute) attribut est déclaré. Il utilise ensuite ces classes et attributs pour générer le manifeste. Considérons par exemple le code suivant : 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Cela ne génère rien dans **fichier AndroidManifest. xml**. Si vous souhaitez qu' `<activity/>` un élément soit généré, vous devez utiliser l’élément[`[Activity]`](xref:Android.App.Activity) 
attribut personnalisé: 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Dans cet exemple, le fragment XML suivant est ajouté à **fichier AndroidManifest. xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

L' `[Activity]` attribut n’a aucun effet `abstract` sur les types; `abstract` les types sont ignorés.



### <a name="activity-name"></a>Nom de l'activité

À partir de Xamarin. Android 5,1, le nom de type d’une activité est basé sur le MD5SUM du nom qualifié d’assembly du type en cours d’exportation. Cela permet de fournir le même nom qualifié complet à partir de deux assemblys différents et de ne pas obtenir d’erreur de Packaging. (Avant Xamarin. Android 5,1, le nom de type par défaut de l’activité a été créé à partir de l’espace de noms en minuscules et du nom de classe.) 

Si vous souhaitez remplacer cette valeur par défaut et spécifier explicitement le nom de votre activité, utilisez la [`Name`](xref:Android.App.ActivityAttribute.Name) propriété: 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment XML suivant:

```xml
<activity android:name="awesome.demo.activity" />
```

*Remarque*: vous devez utiliser la `Name` propriété uniquement pour des raisons de compatibilité descendante, car ce changement de nom peut ralentir la recherche de type au moment de l’exécution. Si vous avez du code hérité qui s’attend à ce que le nom de type par défaut de l’activité soit basé sur l’espace de noms en minuscules et le nom de la classe, consultez attribution d’un nom à un [Wrapper Android pouvant être appelé](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) pour obtenir des conseils sur la gestion de la compatibilité. 


### <a name="activity-title-bar"></a>Barre de titre de l’activité

Par défaut, Android donne à votre application une barre de titre lorsqu’elle est exécutée. La valeur utilisée pour ce est [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label). Dans la plupart des cas, cette valeur diffère du nom de votre classe. Pour spécifier l’étiquette de votre application dans la barre de titre, [`Label`](xref:Android.App.ActivityAttribute.Label) utilisez la propriété.
Par exemple : 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment XML suivant:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```


### <a name="launchable-from-application-chooser"></a>Lancement à partir du sélecteur d’application

Par défaut, votre activité ne s’affiche pas dans l’écran du lanceur d’applications Android. Cela est dû au fait qu’il y aura probablement de nombreuses activités dans votre application et que vous ne voulez pas une icône pour chacune d’elles. Pour spécifier celui qui doit être lancé à partir du lanceur d’applications, [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) utilisez la propriété. Par exemple : 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment XML suivant:

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```



### <a name="activity-icon"></a>Icône d’activité

Par défaut, votre activité reçoit l’icône de lancement par défaut fournie par le système. Pour utiliser une icône personnalisée, ajoutez d’abord votre **. png** à **ressources/dessinable**, définissez son action de génération sur **AndroidResource**, puis [`Icon`](xref:Android.App.ActivityAttribute.Icon) utilisez la propriété pour spécifier l’icône à utiliser. Par exemple : 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment XML suivant:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
</activity>
```


### <a name="permissions"></a>Autorisations

Lorsque vous ajoutez des autorisations au manifeste Android (comme décrit dans [Ajouter des autorisations au manifeste Android](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), ces autorisations sont enregistrées dans **Propriétés/fichier AndroidManifest. xml**. Par exemple, si vous définissez l' `INTERNET` autorisation, l’élément suivant est ajouté à **Properties/fichier AndroidManifest. xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Les versions Debug définissent automatiquement certaines autorisations pour faciliter le débogage `READ_EXTERNAL_STORAGE`( &ndash; `INTERNET` comme et). ces paramètres sont définis uniquement dans le **fichier OBJ/Debug/Android/fichier AndroidManifest. xml** généré et ne sont pas activés dans la Paramètres d' **autorisation requis** . 

Par exemple, si vous examinez le fichier manifeste généré à l’adresse **obj/Debug/Android/fichier AndroidManifest. xml**, vous pouvez voir les éléments d’autorisation ajoutés suivants: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Dans la version Release du manifeste (à l’adresse **obj/Debug/Android/fichier AndroidManifest. xml**), ces autorisations ne sont *pas* configurées automatiquement. Si vous constatez que le basculement vers une version Release provoque la perte par votre application d’une autorisation qui était disponible dans la version Debug, vérifiez que vous avez explicitement défini cette autorisation dans les paramètres d' **autorisations nécessaires** pour votre application (voir **Build > Android Application** dans Visual Studio pour Mac; consultez **propriétés > manifeste Android** dans Visual Studio). 




## <a name="advanced-features"></a>Fonctionnalités avancées


### <a name="intent-actions-and-features"></a>Actions et fonctionnalités d’intention

Le manifeste Android vous permet de décrire les fonctionnalités de votre activité. Cette opération s’effectue par le biais d' [intentions](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) et de la[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
attribut personnalisé. Vous pouvez spécifier les actions appropriées pour votre activité avec la[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
et les catégories appropriées avec l'[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. Au moins une activité doit être fournie (c’est pourquoi les activités sont fournies dans le constructeur). `[IntentFilter]`peut être fourni plusieurs fois, et chaque utilisation produit un élément distinct `<intent-filter/>` dans le. `<activity/>` Par exemple :

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment XML suivant:

```xml
<activity android:icon="@drawable/myicon" android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity">
  <intent-filter>
    <action android:name="android.intent.action.MAIN" />
    <category android:name="android.intent.category.LAUNCHER" />
  </intent-filter>
  <intent-filter>
    <action android:name="android.intent.action.VIEW" />
    <category android:name="android.intent.category.SAMPLE_CODE" />
    <category android:name="my.custom.category" />
  </intent-filter>
</activity>
```


### <a name="application-element"></a>Élément application

Le manifeste Android vous offre également la possibilité de déclarer des propriétés pour l’ensemble de votre application. Cette opération s’effectue par `<application>` le biais de l’élément et de son équivalent, l’attribut personnalisé de l' [application](xref:Android.App.ApplicationAttribute) . Notez qu’il s’agit de paramètres à l’ensemble de l’application (au niveau de l’assembly) plutôt que de paramètres par activité. En règle générale, `<application>` vous déclarez des propriétés pour l’ensemble de votre application, puis vous remplacez ces paramètres (si nécessaire) en fonction de chaque activité. 

Par exemple, l’attribut `Application` suivant est ajouté à **AssemblyInfo.cs** pour indiquer que l’application peut être déboguée, que son nom lisible par l’utilisateur est **mon application**et qu’il utilise le `Theme.Light` style comme thème par défaut pour tous les ACTIVITE 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Cette déclaration provoque la génération du fragment XML suivant dans **obj/Debug/Android/fichier AndroidManifest. xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```
Dans cet exemple, toutes les activités de l’application s’affichent `Theme.Light` par défaut dans le style. Si vous définissez le thème d’une activité `Theme.Dialog`sur, seule cette activité utilisera `Theme.Dialog` le style, tandis que toutes les autres activités de votre `Theme.Light` application auront comme valeur par `<application>` défaut le style défini dans l’élément. 

L' `Application` élément n’est pas la seule façon de `<application>` configurer des attributs. Vous pouvez également insérer des attributs directement dans l' `<application>` élément de **Properties/fichier AndroidManifest. xml**. Ces paramètres sont fusionnés dans le dernier `<application>` élément qui réside dans **obj/Debug/Android/fichier AndroidManifest. xml**. Notez que le contenu de **Properties/fichier AndroidManifest. xml** remplace toujours les données fournies par les attributs personnalisés. 

Il existe de nombreux attributs à l’ensemble de l’application que vous `<application>` pouvez configurer dans l’élément. pour plus d’informations sur ces paramètres, consultez la section [propriétés publiques](xref:Android.App.ApplicationAttribute) de [ApplicationAttribute](xref:Android.App.ApplicationAttribute). 



## <a name="list-of-custom-attributes"></a>Liste des attributs personnalisés

-   [Android. app. ActivityAttribute](xref:Android.App.ActivityAttribute) : Génère un fragment XML [/Manifest/application/Activity](https://developer.android.com/guide/topics/manifest/activity-element.html) 
-   [Android. app. ApplicationAttribute](xref:Android.App.ApplicationAttribute) : Génère un fragment XML [/Manifest/application](https://developer.android.com/guide/topics/manifest/application-element.html) 
-   [Android. app. InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : Génère un fragment XML [/Manifest/instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
-   [Android. app. IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : Génère un fragment XML [//Intent-Filter](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
-   [Android. app. MetaDataAttribute](xref:Android.App.MetaDataAttribute) : Génère un fragment XML [//Meta-Data](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
-   [Android. app. PermissionAttribute](xref:Android.App.PermissionAttribute) : Génère un fragment XML [//permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
-   [Android. app. PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : Génère un fragment XML [//permission-Group](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
-   [Android. app. PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : Génère un fragment XML [//permission-Tree](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
-   [Android. app. ServiceAttribute](xref:Android.App.ServiceAttribute) : Génère un fragment XML [/Manifest/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) 
-   [Android. app. UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : Génère un fragment XML [/Manifest/application/uses-Library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) 
-   [Android. app. UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : Génère un fragment XML [/Manifest/uses-permission](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
-   [Android. Content. BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : Génère un fragment XML [/Manifest/application/Receiver](https://developer.android.com/guide/topics/manifest/receiver-element.html) 
-   [Android. Content. ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : Génère un fragment XML [/Manifest/application/Provider](https://developer.android.com/guide/topics/manifest/provider-element.html) 
-   [Android. Content. GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : Génère un fragment XML [/Manifest/application/Provider/Grant-URI-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html)

