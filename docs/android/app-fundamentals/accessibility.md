---
title: Accessibilité sur Android
ms.prod: xamarin
ms.assetid: 157F0899-4E3E-4538-90AF-B59B8A871204
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 02/28/2018
ms.openlocfilehash: 982d5b81a22d6e69227081420a5947aed4d3aab1
ms.sourcegitcommit: 9bfedf07940dad7270db86767eb2cc4007f2a59f
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/21/2019
ms.locfileid: "70755683"
---
# <a name="accessibility-on-android"></a>Accessibilité sur Android

Cette page explique comment utiliser les API d’accessibilité Android pour créer des applications en fonction de la [liste de contrôle d’accessibilité](~/cross-platform/app-fundamentals/accessibility.md).
Reportez-vous aux pages d’accessibilité [iOS](~/ios/app-fundamentals/accessibility.md) et [OS X](~/mac/app-fundamentals/accessibility.md) pour d’autres API de plateforme.

## <a name="describing-ui-elements"></a>Description des éléments d’interface utilisateur

Android fournit une propriété de `ContentDescription` qui est utilisée par les API de lecture d’écran pour fournir une description accessible de l’objectif du contrôle.

La description du contenu peut être définie dans C# ou dans le fichier de disposition AXML.

**C#**

La description peut être définie dans le code sur n’importe quelle chaîne (ou ressource de type chaîne) :

```csharp
saveButton.ContentDescription = "Save data";
```

**Disposition AXML**

Dans les dispositions XML, utilisez l’attribut `android:contentDescription` :

```xml
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="Save data" />
```

### <a name="use-hint-for-textview"></a>Utiliser l’indicateur pour TextView

Pour les contrôles `EditText` et `TextView` pour l’entrée de données, utilisez la propriété `Hint` pour fournir une description de l’entrée attendue (au lieu de `ContentDescription`).
Quand un texte est entré, le texte lui-même est lu à la place de l’indicateur.

**C#**

Définissez la propriété `Hint` dans le code :

```csharp
someText.Hint = "Enter some text"; // displays (and is "read") when control is empty
```

**Disposition AXML**

Dans les fichiers de disposition XML, utilisez l’attribut `android:hint` :

```xml
<EditText
    android:id="@+id/someText"
    android:hint="Enter some text" />
```

### <a name="labelfor-links-input-fields-with-labels"></a>LabelFor lie les champs d’entrée aux étiquettes

Pour associer une étiquette à un contrôle d’entrée de données, utilisez la propriété `LabelFor` pour

**C#**

Dans C#, affectez à la propriété `LabelFor` l’ID de ressource du contrôle que ce contenu décrit (en général, cette propriété est définie sur une étiquette et fait référence à un autre contrôle d’entrée) :

```csharp
EditText edit = FindViewById<EditText> (Resource.Id.editFirstName);
TextView tv = FindViewById<TextView> (Resource.Id.labelFirstName);
tv.LabelFor = Resource.Id.editFirstName;
```

**Disposition AXML**

Dans la disposition XML, utilisez la propriété `android:labelFor` pour référencer l’identificateur d’un autre contrôle :

```xml
<TextView
    android:id="@+id/labelFirstName"
    android:hint="Enter some text"
    android:labelFor="@+id/editFirstName" />
<EditText
    android:id="@+id/editFirstName"
    android:hint="Enter some text" />
```

### <a name="announce-for-accessibility"></a>Annoncer l’accessibilité

Utilisez la méthode `AnnounceForAccessibility` sur n’importe quel contrôle d’affichage pour communiquer un événement ou une modification d’État aux utilisateurs lorsque l’accessibilité est activée. Cette méthode n’est pas obligatoire pour la plupart des opérations où le discours intégré fournit des commentaires suffisants, mais doit être utilisé lorsque des informations supplémentaires sont utiles pour l’utilisateur.

Le code ci-dessous montre un exemple simple appelant `AnnounceForAccessibility` :

```csharp
button.Click += delegate {
  button.Text = string.Format ("{0} clicks!", count++);
  button.AnnounceForAccessibility (button.Text);
};
```

## <a name="changing-focus-settings"></a>Modification des paramètres de focus

La navigation accessible s’appuie sur les contrôles qui ont le focus pour aider l’utilisateur à comprendre quelles opérations sont disponibles. Android fournit une propriété `Focusable` qui peut baliser des contrôles en vue de leur permettre de recevoir le focus lors de la navigation.

**C#**

Pour empêcher un contrôle de se concentrer sur C#, affectez à la propriété `Focusable` la valeur `false` :

```csharp
label.Focusable = false;
```

**Disposition AXML**

Dans disposition des fichiers XML, définissez l’attribut `android:focusable` :

```xml
<android:focusable="false" />
```

Vous pouvez également contrôler l’ordre de focus avec les attributs `nextFocusDown`, `nextFocusLeft`, `nextFocusRight` `nextFocusUp`, généralement définis dans la disposition AXML. Utilisez ces attributs pour vous assurer que l’utilisateur peut naviguer facilement dans les contrôles à l’écran.

## <a name="accessibility-and-localization"></a>Accessibilité et localisation

Dans les exemples ci-dessus, l’indicateur et la description du contenu sont définis directement sur la valeur d’affichage. Il est préférable d’utiliser des valeurs dans un fichier **Strings. xml** , par exemple :

```xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string name="enter_info">Enter some text</string>
    <string name="save_info">Save data</string>
</resources>
```

L’utilisation de texte à partir d’un fichier de C# chaînes est illustrée ci-dessous dans les fichiers de disposition et AXML :

**C#**

Au lieu d’utiliser des littéraux de chaîne dans le code, recherchez les valeurs traduites à partir de fichiers de chaînes avec `Resources.GetText` :

```csharp
someText.Hint = Resources.GetText (Resource.String.enter_info);
saveButton.ContentDescription = Resources.GetText (Resource.String.save_info);
```

**AXML**

Dans disposition, les attributs d’accessibilité XML comme `hint` et `contentDescription` peuvent être définis sur un identificateur de chaîne :

```xml
<TextView
    android:id="@+id/someText"
    android:hint="@string/enter_info" />
<ImageButton
    android:id=@+id/saveButton"
    android:src="@drawable/save_image"
    android:contentDescription="@string/save_info" />
```

L’avantage de stocker du texte dans un fichier distinct est que plusieurs traductions de langue du fichier peuvent être fournies dans votre application. Consultez le [Guide de localisation Android](~/android/app-fundamentals/localization.md) pour savoir comment ajouter des fichiers de chaîne localisés à un projet d’application.

## <a name="testing-accessibility"></a>Test d’accessibilité

Procédez comme [suit](https://developer.android.com/training/accessibility/testing.html#how-to) pour activer Talkback et Explorer par Touch pour tester l’accessibilité sur des appareils Android.

Vous devrez peut-être installer [Talkback](https://play.google.com/store/apps/details?id=com.google.android.marvin.talkback) à partir de Google Play s’il n’apparaît pas dans **Paramètres > accessibilité**.

## <a name="related-links"></a>Liens associés

- [Accessibilité multiplateforme](~/cross-platform/app-fundamentals/accessibility.md)
- [API d’accessibilité Android](https://developer.android.com/guide/topics/ui/accessibility/index.html)
