---
title: Travailler avec le Manifeste Android
ms.prod: xamarin
ms.assetid: CB7CCF60-FEF1-3B28-215F-159391E74347
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 02/05/2018
ms.openlocfilehash: 1438c012608b367c21ebcc401c058b186b917f53
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027802"
---
# <a name="working-with-the-android-manifest"></a>Travailler avec le Manifeste Android

**AndroidManifest.xml** est un fichier puissant dans la plate-forme Android qui vous permet de décrire la fonctionnalité et les exigences de votre application sur Android. Cependant, travailler avec elle n’est pas facile. Xamarin.Android aide à minimiser cette difficulté en vous permettant d’ajouter des attributs personnalisés à vos classes, qui seront ensuite utilisés pour générer automatiquement le manifeste pour vous. Notre objectif est que 99% de nos utilisateurs ne devraient jamais avoir besoin de modifier manuellement **AndroidManifest.xml**. 

**AndroidManifest.xml** est généré dans le cadre du processus de construction, et le XML trouvé dans **Properties/AndroidManifest.xml** est fusionné avec XML qui est généré à partir d’attributs personnalisés. **L’AndroidManifest.xml** fusionné qui en résulte réside dans la sous-directionnelle **obj;** par exemple, il réside à **obj/Debug/android/AndroidManifest.xml** pour debug construit. Le processus de fusion est trivial: il utilise des attributs personnalisés dans le code pour générer des éléments XML, et *insère* ces éléments dans **AndroidManifest.xml**. 

## <a name="the-basics"></a>Concepts de base

Au moment de la compilation, les assemblages sont numérisés pour les classes non-classes`abstract` qui dérivent de [l’activité](xref:Android.App.Activity) et ont l’attribut [`[Activity]`](xref:Android.App.ActivityAttribute) déclaré sur eux. Il utilise ensuite ces classes et attributs pour construire le manifeste. Considérons par exemple le code suivant : 

```csharp
namespace Demo
{
    public class MyActivity : Activity
    {
    }
}
```

Cela n’a comme conséquence rien généré dans **AndroidManifest.xml**. Si vous `<activity/>` voulez qu’un élément soit généré, vous devez[`[Activity]`](xref:Android.App.Activity) 
attribut personnalisé : 

```csharp
namespace Demo
{
    [Activity]
    public class MyActivity : Activity
    {
    }
}
```

Cet exemple provoque l’ajout du fragment xml suivant à **AndroidManifest.xml**:

```xml
<activity android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

L’attribut `[Activity]` n’a aucun effet sur `abstract` les types; `abstract` types sont ignorés.

### <a name="activity-name"></a>Nom de l’activité

En commençant par Xamarin.Android 5.1, le nom type d’une activité est basé sur le MD5SUM du nom d’assemblage qualifié du type exporté. Cela permet de fournir le même nom entièrement qualifié à partir de deux assemblages différents et de ne pas obtenir une erreur d’emballage. (Avant Xamarin.Android 5.1, le nom de type par défaut de l’activité a été créé à partir de l’espace de nom minuscule et le nom de classe.) 

Si vous souhaitez passer outre à ce défaut et spécifier explicitement le nom de votre activité, utilisez la [`Name`](xref:Android.App.ActivityAttribute.Name) propriété : 

```csharp
[Activity (Name="awesome.demo.activity")]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment xml suivant :

```xml
<activity android:name="awesome.demo.activity" />
```

> [!NOTE]
> Vous devez `Name` utiliser la propriété uniquement pour des raisons de compatibilité vers l’arrière, car un tel changement de nom peut ralentir la recherche de type à l’heure d’exécution. Si vous avez un code hérité qui s’attend à ce que le nom de type par défaut de l’activité soit basé sur l’espace de nom inférieur et le nom de la classe, voir [Android Callable Wrapper Naming](https://github.com/xamarin/release-notes-archive/blob/master/release-notes/android/xamarin.android_5/xamarin.android_5.1/index.md#Android_Callable_Wrapper_Naming) pour des conseils sur le maintien de la compatibilité. 

### <a name="activity-title-bar"></a>Barre de titre d’activité

Par défaut, Android donne à votre application une barre de titre lorsqu’elle est exécutée. La valeur utilisée [`/manifest/application/activity/@android:label`](https://developer.android.com/guide/topics/manifest/activity-element.html#label)pour cela est . Dans la plupart des cas, cette valeur sera différente de votre nom de classe. Pour spécifier l’étiquette de votre [`Label`](xref:Android.App.ActivityAttribute.Label) application sur la barre de titre, utilisez la propriété.
Par exemple : 

```csharp
[Activity (Label="Awesome Demo App")]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment xml suivant :

```xml
<activity android:label="Awesome Demo App" 
          android:name="md5a7a3c803e481ad8926683588c7e9031b.MainActivity" />
```

### <a name="launchable-from-application-chooser"></a>Lancement de l’application Chooser

Par défaut, votre activité ne s’affichera pas dans l’écran de lancement d’applications d’Android. C’est parce qu’il y aura probablement beaucoup d’activités dans votre application, et vous ne voulez pas une icône pour chacun. Pour spécifier lequel doit être lancé à [`MainLauncher`](xref:Android.App.ActivityAttribute.MainLauncher) partir du lanceur d’applications, utilisez la propriété. Par exemple : 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true)] 
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment xml suivant :

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

Par défaut, votre activité recevra l’icône de lanceur par défaut fournie par le système. Pour utiliser une icône personnalisée, ajoutez d’abord votre **.png** à **Resources/drawable,** définissez son action de construction à **AndroidResource,** puis utilisez la [`Icon`](xref:Android.App.ActivityAttribute.Icon) propriété pour spécifier l’icône à utiliser. Par exemple : 

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment xml suivant :

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

Lorsque vous ajoutez des autorisations à l’Android Manifest (tel que décrit dans [Add Permissions to Android Manifest](https://github.com/xamarin/recipes/tree/master/Recipes/android/general/projects/add_permissions_to_android_manifest)), ces autorisations sont enregistrées dans **Properties/AndroidManifest.xml**. Par exemple, si `INTERNET` vous définissez la permission, l’élément suivant est ajouté à **Properties/AndroidManifest.xml**: 

```xml
<uses-permission android:name="android.permission.INTERNET" />
```

Debug construit automatiquement définir certaines autorisations pour rendre `INTERNET` débog plus facile (tels `READ_EXTERNAL_STORAGE`que et ) &ndash; ces paramètres sont définis uniquement dans l’obj/Debug/android/AndroidManifest.xml et ne sont pas indiqués comme activés dans les paramètres **d’autorisation requis.** **obj/Debug/android/AndroidManifest.xml** 

Par exemple, si vous examinez le fichier manifeste généré à **obj/Debug/android/AndroidManifest.xml**, vous pouvez voir les éléments d’autorisation supplémentaires suivants : 

```xml
<uses-permission android:name="android.permission.INTERNET" />
<uses-permission android:name="android.permission.READ_EXTERNAL_STORAGE" />
```

Dans la version de version de version de sortie du manifeste (à **obj/Debug/android/AndroidManifest.xml**), ces autorisations ne sont *pas* automatiquement configurées. Si vous constatez que le passage à une version de version fait perdre à votre application une autorisation disponible dans la version Debug, vérifiez que vous avez explicitement défini cette autorisation dans les paramètres **d’autorisation requis** pour votre application (voir **Build > Android Application** dans Visual Studio pour Mac; voir Properties > Android **Manifest** dans Visual Studio). 

## <a name="advanced-features"></a>Fonctionnalités avancées

### <a name="intent-actions-and-features"></a>Actions et caractéristiques d’intention

Le manifeste Android vous permet de décrire les capacités de votre activité. Cela se fait par [l’intermédiaire d’Intents](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) et[`[IntentFilter]`](xref:Android.App.IntentFilterAttribute)
attribut personnalisé. Vous pouvez spécifier quelles actions sont appropriées pour votre activité[`IntentFilter`](xref:Android.App.IntentFilterAttribute#ctor*)
et quelles catégories sont appropriées avec le[`Categories`](xref:Android.App.IntentFilterAttribute.Categories)
. Au moins une activité doit être fournie (c’est pourquoi des activités sont fournies dans le constructeur). `[IntentFilter]`peut être fourni plusieurs fois, et `<intent-filter/>` chaque utilisation `<activity/>`donne lieu à un élément distinct dans le . Par exemple :

```csharp
[Activity (Label="Awesome Demo App", MainLauncher=true, Icon="@drawable/myicon")] 
[IntentFilter (new[]{Intent.ActionView}, 
        Categories=new[]{Intent.CategorySampleCode, "my.custom.category"})]
public class MyActivity : Activity
{
}
```

Cet exemple produit le fragment xml suivant :

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

### <a name="application-element"></a>Élément d’application

Le manifeste Android fournit également un moyen pour vous de déclarer des propriétés pour l’ensemble de votre application. Ceci est fait `<application>` via l’élément et son homologue, [l’attribut personnalisé d’application.](xref:Android.App.ApplicationAttribute) Notez qu’il s’agit de paramètres à l’échelle de l’application (à l’échelle de l’assemblage) plutôt que de paramètres par activité. En règle `<application>` générale, vous déclarez des propriétés pour l’ensemble de votre application, puis remplacez ces paramètres (au besoin) sur une base par activité. 

Par exemple, `Application` l’attribut suivant est ajouté à **AssemblyInfo.cs** pour indiquer que l’application peut être débogée, que `Theme.Light` son nom lisible à l’utilisateur est My **App**, et qu’il utilise le style comme thème par défaut pour toutes les activités: 

```csharp
[assembly: Application (Debuggable=true,   
                        Label="My App",   
                        Theme="@android:style/Theme.Light")]
```

Cette déclaration provoque le fragment XML suivant à être généré dans **obj/Debug/android/AndroidManifest.xml**:

```xml
<application android:label="My App" 
             android:debuggable="true" 
             android:theme="@android:style/Theme.Light"
                ... />
```

Dans cet exemple, toutes les activités `Theme.Light` de l’application seront par défaut pour le style. Si vous définissez le `Theme.Dialog`thème d’une activité `Theme.Dialog` à , seulement que l’activité `Theme.Light` utilisera le `<application>` style tandis que toutes les autres activités de votre application seront par défaut au style tel qu’indiqué dans l’élément. 

L’élément `Application` n’est pas `<application>` la seule façon de configurer les attributs. Alternativement, vous pouvez insérer `<application>` des attributs directement dans l’élément de **Propriétés/ AndroidManifest.xml**. Ces paramètres sont fusionnés `<application>` dans l’élément final qui réside dans **obj/Debug/android/AndroidManifest.xml**. Notez que le contenu de **Properties/AndroidManifest.xml** l’emporte toujours sur les données fournies par des attributs personnalisés. 

Il existe de nombreux attributs à l’échelle de l’application que vous pouvez configurer dans l’élément; `<application>` pour plus d’informations sur ces paramètres, consultez la section [Propriétés publiques](xref:Android.App.ApplicationAttribute) d’ApplicationAttribute . [ApplicationAttribute](xref:Android.App.ApplicationAttribute) 

## <a name="list-of-custom-attributes"></a>Liste des attributs personnalisés

- [Android.App.ActivityAttribute](xref:Android.App.ActivityAttribute) : Génère un fragment [/manifeste/application/activité](https://developer.android.com/guide/topics/manifest/activity-element.html) XML 
- [Android.App.ApplicationAttribute](xref:Android.App.ApplicationAttribute) : Génère un fragment [XML /manifeste/application](https://developer.android.com/guide/topics/manifest/application-element.html) 
- [Android.App.InstrumentationAttribute](xref:Android.App.InstrumentationAttribute) : Génère un fragment [XML /manifeste/instrumentation](https://developer.android.com/guide/topics/manifest/instrumentation-element.html) 
- [Android.App.IntentFilterAttribute](xref:Android.App.IntentFilterAttribute) : Génère un fragment XML [//intention-filtre](https://developer.android.com/guide/topics/manifest/intent-filter-element.html) 
- [Android.App.MetaDataAttribute](xref:Android.App.MetaDataAttribute) : Génère un fragment XML [//méta-données](https://developer.android.com/guide/topics/manifest/meta-data-element.html) 
- [Android.App.PermissionAttribute](xref:Android.App.PermissionAttribute) : Génère un fragment [XML //permission](https://developer.android.com/guide/topics/manifest/permission-element.html) 
- [Android.App.PermissionGroupAttribute](xref:Android.App.PermissionGroupAttribute) : Génère un fragment [XML //autorisation-groupe](https://developer.android.com/guide/topics/manifest/permission-group-element.html) 
- [Android.App.PermissionTreeAttribute](xref:Android.App.PermissionTreeAttribute) : Génère un fragment XML [//autorisation-arbre](https://developer.android.com/guide/topics/manifest/permission-tree-element.html) 
- [Android.App.ServiceAttribute](xref:Android.App.ServiceAttribute) : Génère un fragment [/manifeste/application/service](https://developer.android.com/guide/topics/manifest/service-element.html) XML 
- [Android.App.UsesLibraryAttribute](xref:Android.App.UsesLibraryAttribute) : Génère un fragment [/manifeste/application/uses-library](https://developer.android.com/guide/topics/manifest/uses-library-element.html) XML 
- [Android.App.UsesPermissionAttribute](xref:Android.App.UsesPermissionAttribute) : Génère un fragment [XML /manifeste/utilisations-autorisation](https://developer.android.com/guide/topics/manifest/uses-permission-element.html) 
- [Android.Content.BroadcastReceiverAttribute](xref:Android.Content.BroadcastReceiverAttribute) : Génère un fragment [/manifeste/application/récepteur](https://developer.android.com/guide/topics/manifest/receiver-element.html) XML 
- [Android.Content.ContentProviderAttribute](xref:Android.Content.ContentProviderAttribute) : Génère un fragment [/manifeste/application/fournisseur](https://developer.android.com/guide/topics/manifest/provider-element.html) XML 
- [Android.Content.GrantUriPermissionAttribute](xref:Android.Content.GrantUriPermissionAttribute) : Génère un fragment [/manifeste/application/provider/grant-uri-permission](https://developer.android.com/guide/topics/manifest/grant-uri-permission-element.html) XML
