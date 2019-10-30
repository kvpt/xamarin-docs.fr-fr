---
title: Hello, Android multi-écran - Démarrage rapide
description: Ce guide en deux parties étend l’application Phoneword de manière à gérer un second écran. Vous allez également y découvrir les composants des applications Android et plonger au cœur de l’architecture Android.
zone_pivot_groups: platform
ms.topic: quickstart
ms.prod: xamarin
ms.assetid: ED99584A-BA3B-429A-AEE5-CF3CB0116762
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 10/05/2018
ms.openlocfilehash: 114373b6c4b194fe6e566255eb09eb82a8208312
ms.sourcegitcommit: 2fbe4932a319af4ebc829f65eb1fb1816ba305d3
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/29/2019
ms.locfileid: "73020966"
---
# <a name="hello-android-multiscreen-quickstart"></a>Hello, Android multi-écran - Démarrage rapide

_Ce guide en deux parties étend l’application Phoneword pour gérer un deuxième écran. En cours de route, les blocs de construction d’applications Android de base sont introduits dans une étude plus approfondie de l’architecture Android._

Dans la partie de ce guide présentant la procédure pas à pas, vous allez ajouter un second écran à l’application [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) de manière à suivre l’historique des numéros appelés à partir de l’application. L’[application finale](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen) proposera un second écran qui affichera les numéros qui ont été «traduits », comme illustré dans la capture d’écran de droite :

[![Capture d’écran de l’exemple d’application](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

La seconde partie, [En profondeur](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md), examine ce que vous avez généré et décrit l’architecture, la navigation et d’autres nouveaux concepts Android que vous avez rencontrés en chemin.

## <a name="requirements"></a>spécifications

Ce guide reprenant là où [Hello, Android](~/android/get-started/hello-android/index.md) s’est arrêté, vous devez avoir terminé [Hello, Android - Démarrage rapide](~/android/get-started/hello-android/hello-android-quickstart.md).
Si vous souhaitez accéder directement à la procédure pas à pas ci-dessous, vous pouvez télécharger la version complète de [Phoneword](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword) (à partir de Hello, Android - Démarrage rapide) et l’utiliser pour démarrer la procédure pas à pas.

## <a name="walkthrough"></a>Procédure pas à pas

Dans cette procédure pas à pas, vous allez ajouter un écran **Historique de traduction** à l’application **Phoneword**.

::: zone pivot="windows"

Commencez par ouvrir l’application **Phoneword** dans Visual Studio et modifier le fichier **Main.axml** à partir de l’**Explorateur de solutions**.

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

### <a name="updating-the-layout"></a>Mise à jour de la disposition

Dans la **Boîte à outils**, faites glisser un **Bouton** sur l’aire de conception et placez-le sous le TextView **TranslatedPhoneWord**. Dans le volet **Propriétés**, remplacez l’**Id** du bouton par `@+id/TranslationHistoryButton`

[![Faire glisser un nouveau bouton](hello-android-multiscreen-quickstart-images/vs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/vs/02-new-button.png#lightbox)

Définissez la propriété **Texte** du bouton sur `@string/translationHistory`. Android Designer interprète ce texte littéralement, mais vous allez apporter quelques modifications afin que le texte du bouton s’affiche correctement :

[![Définir le texte du bouton d’historique de traduction](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string-sml.png)](hello-android-multiscreen-quickstart-images/vs/03-translation-history-string.png#lightbox)

Développez le nœud **valeurs** dans le dossier **Ressources** de l’**Explorateur de solutions** et double-cliquez sur le fichier de ressources de chaîne, **Strings.xml** :

[![Ouvrir Strings.xml](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/04-strings-resources-file.png#lightbox)

Ajoutez le nom de chaîne `translationHistory` et sa valeur au fichier **Strings.xml** et enregistrez-le :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Le texte du bouton **Historique de traduction** doit se mettre à jour pour refléter la nouvelle valeur de chaîne :

[![Le bouton reflète la nouvelle valeur de chaîne](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png)](hello-android-multiscreen-quickstart-images/vs/05-new-string-value.png#lightbox)

Sélectionnez le bouton **Historique de traduction** sur l’aire de conception, puis recherchez le paramètre `enabled` dans le volet **Propriétés** et définissez sa valeur sur `false` pour désactiver le bouton. Le bouton devient alors plus sombre sur l’aire de conception :

[![Désactiver le bouton d’historique de traduction](hello-android-multiscreen-quickstart-images/vs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/vs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Création de la deuxième activité

Créez une seconde activité pour activer le second écran. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **Phoneword**, puis choisissez **Ajouter > Nouvel élément...**  :

[![Ajouter un nouveau fichier](hello-android-multiscreen-quickstart-images/vs/07-add-new-file-sml.png)](hello-android-multiscreen-quickstart-images/vs/07-add-new-file.png#lightbox)

Dans la boîte de dialogue **Ajouter un nouvel élément**, choisissez **Visual c# > Activité** et nommez le fichier d’activité **ActivitéHistoriqueTraduction.cs**.

Remplacez le modèle de code dans **ActivitéHistoriqueTraduction.cs** par le suivant :

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

Dans cette classe, vous créez un élément `ListActivity` et le remplissez par programmation. Vous n’avez donc pas besoin de créer un nouveau fichier de disposition pour cette activité. Ce sujet est abordé plus en détail dans [Hello, Android multi-écran - En profondeur](~/android/get-started/hello-android/hello-android-deepdive.md).

### <a name="adding-a-list"></a>Ajout d’une liste

Cette application collecte les numéros de téléphone (que l’utilisateur a traduits dans le premier écran) et les transmet au second écran. Les numéros de téléphone sont stockés sous forme de liste de chaînes. Pour prendre en charge les listes (et intentions, qui sont utilisées ultérieurement), ajoutez les directives `using` suivantes en haut de **MainActivity.cs** :

```csharp
using System.Collections.Generic;
using Android.Content;
```

Ensuite, créez une liste vide qui peut être remplie avec des numéros de téléphone.
La classe `MainActivity` se présentera comme suit :

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Dans la classe `MainActivity`, ajoutez le code suivant pour inscrire le bouton **Historique de traduction** (placez cette ligne après la déclaration `translateButton`) :

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Ajoutez le code suivant à la fin de la méthode `OnCreate` pour associer le bouton **Historique de traduction** :

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Mettez à jour le bouton **Traduire** pour ajouter le numéro de téléphone à la liste des `phoneNumbers`. La handler `Click` de `translateButton` doit être semblable au code suivant :

```csharp
// Add code to translate number
string translatedNumber = string.Empty;
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

Enregistrez et générez l’application pour vérifier qu’il n’y a pas d’erreurs.

### <a name="running-the-app"></a>Exécution de l’application

Déployez l’application sur un émulateur ou un appareil. Les captures d’écran suivantes illustrent l’application **Phoneword** en cours d’exécution :

[![Exemples de captures d’écran](hello-android-multiscreen-quickstart-images/screenshot-sml.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end
::: zone pivot="macos"

Commencez par ouvrir l’application **Phoneword** dans Visual Studio pour Mac et modifier le fichier **Main.axml** dans le **Panneau Solutions**.

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

### <a name="updating-the-layout"></a>Mise à jour de la disposition

Dans la **Boîte à outils**, faites glisser un **Bouton** sur l’aire de conception et placez-le sous le TextView **TranslatedPhoneWord**. Dans le Panneau **Propriétés**, remplacez l’**ID** du bouton par `@+id/TranslationHistoryButton` :

[![Faire glisser un nouveau bouton](hello-android-multiscreen-quickstart-images/xs/02-new-button-sml.png)](hello-android-multiscreen-quickstart-images/xs/02-new-button.png#lightbox)

Définissez la propriété **Texte** du bouton sur `@string/translationHistory`. Android Designer interprète ce texte littéralement, mais vous allez apporter quelques modifications afin que le texte du bouton s’affiche correctement :

[![Définir le texte du bouton d’historique de traduction](hello-android-multiscreen-quickstart-images/xs/03-call-history-string-sml.png)](hello-android-multiscreen-quickstart-images/xs/03-call-history-string.png#lightbox)

Développez le nœud **valeurs** dans le dossier **Ressources** du **Panneau Solutions** et double-cliquez sur le fichier de ressources de chaîne, **Strings.xml** :

[![Ouvrir les chaînes](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file-sml.png)](hello-android-multiscreen-quickstart-images/xs/04-strings-resources-file.png#lightbox)

Ajoutez le nom de chaîne `translationHistory` et sa valeur au fichier **Strings.xml** et enregistrez-le :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="translationHistory">Translation History</string>
    <string name="ApplicationName">Phoneword</string>
</resources>
```

Le texte du bouton **Historique de traduction** doit se mettre à jour pour refléter la nouvelle valeur de chaîne :

[![Le bouton reflète la nouvelle valeur de chaîne](hello-android-multiscreen-quickstart-images/xs/05-new-string-value-sml.png)](hello-android-multiscreen-quickstart-images/xs/05-new-string-value.png#lightbox)

Sélectionnez le bouton **Historique de traduction** sur l’aire de conception, placez-vous sous l’onglet **Comportement** dans le **Panneau Propriétés**, puis double-cliquez sur la case à cocher **Activé** pour désactiver le bouton. Le bouton devient alors plus sombre sur l’aire de conception :

[![Désactiver le bouton d’historique de traduction](hello-android-multiscreen-quickstart-images/xs/06-enabled-false-sml.png)](hello-android-multiscreen-quickstart-images/xs/06-enabled-false.png#lightbox)

### <a name="creating-the-second-activity"></a>Création de la deuxième activité

Créez une seconde activité pour activer le second écran. Dans le **Panneau Solutions**, cliquez sur l’icône d’engrenage grise en regard du projet **Phoneword** et choisissez **Ajouter > nouveau fichier...**  :

Dans la boîte de dialogue **Nouveau fichier**, choisissez **Android > Activité**, nommez l’activité `TranslationHistoryActivity`, puis cliquez sur **Ajouter**.

Remplacez le code du modèle dans `TranslationHistoryActivity` par le suivant :

```csharp
using System;
using System.Collections.Generic;
using Android.App;
using Android.OS;
using Android.Widget;
namespace Phoneword
{
    [Activity(Label = "@string/translationHistory")]
    public class TranslationHistoryActivity : ListActivity
    {
        protected override void OnCreate(Bundle bundle)
        {
            base.OnCreate(bundle);
            // Create your application here
            var phoneNumbers = Intent.Extras.GetStringArrayList("phone_numbers") ?? new string[0];
            this.ListAdapter = new ArrayAdapter<string>(this, Android.Resource.Layout.SimpleListItem1, phoneNumbers);
        }
    }
}
```

Dans cette classe, un élément `ListActivity` est créé et rempli par programmation. Vous n’avez donc pas besoin de créer un nouveau fichier de disposition pour cette activité. Ce sujet est abordé plus en détail dans [Hello, Android multi-écran - En profondeur](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

### <a name="adding-a-list"></a>Ajout d’une liste

Cette application collecte les numéros de téléphone (que l’utilisateur a traduits dans le premier écran) et les transmet au second écran. Les numéros de téléphone sont stockés sous forme de liste de chaînes. Pour prendre en charge les listes (et intentions, qui sont utilisées ultérieurement), ajoutez les directives `using` suivantes en haut de **MainActivity.cs** :

```csharp
using System.Collections.Generic;
using Android.Content;
```

Ensuite, créez une liste vide qui peut être remplie avec des numéros de téléphone. La classe `MainActivity` se présentera comme suit :

```csharp
[Activity(Label = "Phoneword", MainLauncher = true)]
public class MainActivity : Activity
{
    static readonly List<string> phoneNumbers = new List<string>();
    ...// OnCreate, etc.
}
```

Dans la classe `MainActivity`, ajoutez le code suivant pour inscrire le bouton **Historique de traduction** (placez cette ligne après la déclaration `TranslationHistoryButton`) :

```csharp
Button translationHistoryButton = FindViewById<Button> (Resource.Id.TranslationHistoryButton);
```

Ajoutez le code suivant à la fin de la méthode `OnCreate` pour associer le bouton **Historique de traduction** :

```csharp
translationHistoryButton.Click += (sender, e) =>
{
    var intent = new Intent(this, typeof(TranslationHistoryActivity));
    intent.PutStringArrayListExtra("phone_numbers", phoneNumbers);
    StartActivity(intent);
};
```

Mettez à jour le bouton **Traduire** pour ajouter le numéro de téléphone à la liste des `phoneNumbers`. La handler `Click` de `TranslateHistoryButton` doit être semblable au code suivant :

```csharp
translateButton.Click += (sender, e) =>
{
    // Translate user's alphanumeric phone number to numeric
    translatedNumber = PhonewordTranslator.ToNumber(phoneNumberText.Text);
    if (string.IsNullOrWhiteSpace(translatedNumber))
    {
        translatedPhoneWord.Text = "";
    }
    else
    {
        translatedPhoneWord.Text = translatedNumber;
        phoneNumbers.Add(translatedNumber);
        translationHistoryButton.Enabled = true;
    }
};
```

### <a name="running-the-app"></a>Exécution de l’application

Déployez l’application sur un émulateur ou un appareil. Les captures d’écran suivantes illustrent l’application **Phoneword** en cours d’exécution :

[![Exemples de captures d’écran](hello-android-multiscreen-quickstart-images/screenshot.png)](hello-android-multiscreen-quickstart-images/screenshot.png#lightbox)

::: zone-end

Félicitations, vous avez terminé votre première application Xamarin.Android multi-écran ! Il est temps maintenant d’étudier en détail les outils et les compétences que vous venez d’acquérir. À suivre : [Hello, Android multi-écran - En profondeur](~/android/get-started/hello-android-multiscreen/hello-android-multiscreen-deepdive.md).

## <a name="related-links"></a>Liens connexes

- [Icônes et écrans de lancement de l’application Xamarin (ZIP)](https://github.com/xamarin/monodroid-samples/blob/master/Phoneword/Resources/XamarinAndroidIcons.zip?raw=true)
- [Phoneword (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phoneword)
- [PhonewordMultiscreen (exemple)](https://docs.microsoft.com/samples/xamarin/monodroid-samples/phonewordmultiscreen)
