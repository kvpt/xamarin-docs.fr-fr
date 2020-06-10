---
titre : « AndroidX migration in Xamarin.Forms » Description : cet article explique pourquoi AndroidX existe et comment migrer vers AndroidX dans votre Xamarin.Forms application.»
ms. Prod : xamarin ms. AssetID : 98884003-E65A-4EB4-842D-66CFE27344A4 ms. Technology : xamarin-Forms auteur : profexorgeek ms. Author : jusjohns ms. Date : 01/22/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="androidx-migration-in-xamarinforms"></a>Migration de AndroidX dansXamarin.Forms

AndroidX remplace la bibliothèque de prise en charge Android. Cet article explique pourquoi AndroidX existe, comment il affecte Xamarin.Forms et comment migrer votre application pour utiliser les bibliothèques AndroidX.

## <a name="history-of-androidx"></a>Historique de AndroidX

La bibliothèque de prise en charge Android a été créée pour fournir des fonctionnalités plus récentes sur les versions antérieures d’Android. Il s’agit d’une couche de compatibilité qui permet aux développeurs d’utiliser des fonctionnalités qui n’existent pas sur toutes les versions du système d’exploitation Android et qui disposent de secours pour des versions antérieures. La bibliothèque de prise en charge comprend également des classes de commodité et d’assistance, des outils de débogage et utilitaires et des classes sophistiquées qui dépendent d’autres classes de bibliothèque de prise en charge pour fonctionner.

Si la bibliothèque de support était à l’origine un fichier binaire unique, elle a évolué et est devenue une suite de bibliothèques, qui sont presque essentielles pour le développement d’applications modernes. Voici quelques-unes des fonctionnalités couramment utilisées à partir de la bibliothèque de prise en charge :

- `Fragment`Classe de prise en charge.
- `RecyclerView`, Utilisé pour gérer des listes longues.
- Prise en charge Multidex pour les applications avec plus de 65 536 méthodes.
- Classe `ActivityCompat`.

AndroidX est un remplacement de la bibliothèque de prise en charge, qui n’est plus conservée-tout le développement de la bibliothèque se produit dans la bibliothèque AndroidX. AndroidX est une bibliothèque repensée qui utilise le contrôle de version sémantique, des noms de packages plus clairs et une meilleure prise en charge des modèles d’architecture d’application courants. AndroidX version 1.0.0 est l’équivalent binaire pour la prise en charge de la version de bibliothèque 28.0.0. Pour obtenir la liste complète des mappages de classes de la bibliothèque de prise en charge à AndroidX, consultez [mappages de classes de bibliothèque de prise en charge](https://developer.android.com/jetpack/androidx/migrate/class-mappings) sur Developer.Android.com.

Google a créé un processus de migration appelé Jetifier avec AndroidX. Le Jetifier inspecte le bytecode jar pendant le processus de génération et remappe les références de la bibliothèque de prise en charge, à la fois dans le code de l’application et dans les dépendances, à leur équivalent AndroidX.

Dans une Xamarin.Forms application, tout comme dans une application Java Android, les dépendances jar doivent être migrées vers AndroidX. Toutefois, les liaisons Xamarin doivent également être migrées pour pointer vers les fichiers jar sous-jacents appropriés. Xamarin.Formsajout de la prise en charge de la migration automatique de AndroidX dans la version 4,5.

Pour plus d’informations sur AndroidX, consultez [vue d’ensemble d’AndroidX](https://developer.android.com/jetpack/androidx) sur Developer.Android.com.

## <a name="automatic-migration-in-xamarinforms"></a>Migration automatique dansXamarin.Forms

Pour migrer automatiquement vers AndroidX, un Xamarin.Forms projet doit :

- Ciblez la version 29 ou ultérieure de l’API Android.
- Utilisez la Xamarin.Forms version 4,5 ou une version ultérieure.

Une fois que vous avez confirmé ces paramètres dans votre projet, générez l’application Android dans Visual Studio 2019. Pendant le processus de génération, le langage intermédiaire (IL, Intermediate Language) est inspecté et les dépendances de bibliothèque et les liaisons sont échangées avec les dépendances de AndroidX. Si votre application a toutes les dépendances AndroidX requises pour la génération, vous ne remarquerez aucune différence dans le processus de génération.

> [!NOTE]
> Vous devez conserver les références à la bibliothèque de prise en charge dans votre projet. Celles-ci sont utilisées pour compiler l’application avant que le processus de migration n’inspecte le IL qui en résulte et transforme les dépendances.

Si des dépendances AndroidX qui ne font pas partie du projet sont détectées, une erreur de build est signalée et indique les packages AndroidX manquants. Un exemple d’erreur de build est illustré ci-dessous :

```
Could not find 37 AndroidX assemblies, make sure to install the following NuGet packages:
- Xamarin.AndroidX.Lifecycle.LiveData
- Xamarin.AndroidX.Browser
- Xamarin.Google.Android.Material
- Xamarin.AndroidX.Legacy.Supportv4
You can also copy and paste the following snippit into your .csproj file:
 <PackageReference Include="Xamarin.AndroidX.Lifecycle.LiveData" Version="2.1.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Browser" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.Google.Android.Material" Version="1.0.0-rc1" />
 <PackageReference Include="Xamarin.AndroidX.Legacy.Support.V4" Version="1.0.0-rc1" />
```

Les packages NuGet manquants peuvent être installés par le biais du gestionnaire de package NuGet dans Visual Studio, ou installés en modifiant votre fichier Android. csproj pour inclure les `PackageReference` éléments XML listés dans l’erreur.

Une fois les packages manquants résolus, la reconstruction du projet charge les packages manquants et votre projet est compilé à l’aide des dépendances AndroidX au lieu de prendre en charge les dépendances de bibliothèque.

> [!NOTE]
> Si votre projet, et les dépendances de projet, ne font pas référence aux bibliothèques de prise en charge Android, le processus de migration ne fait rien et n’est pas exécuté.

## <a name="related-links"></a>Liens connexes

- [Vue d’ensemble de la bibliothèque de support Android](https://developer.android.com/topic/libraries/support-library/index) sur Developer.Android.com
- [Présentation de AndroidX](https://developer.android.com/jetpack/androidx) sur Developer.Android.com
