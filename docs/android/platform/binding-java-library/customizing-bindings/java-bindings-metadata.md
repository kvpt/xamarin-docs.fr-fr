---
title: Métadonnées de liaisons Java
description: C#le code dans Xamarin. Android appelle des bibliothèques Java via des liaisons, qui sont un mécanisme qui soustrait les détails de bas niveau qui sont spécifiés dans JNI (Java Native Interface). Xamarin. Android fournit un outil qui génère ces liaisons. Cet outil permet au développeur de contrôler la façon dont une liaison est créée à l’aide de métadonnées, ce qui permet des procédures telles que la modification des espaces de noms et le changement de nom des membres. Ce document décrit le fonctionnement des métadonnées, résume les attributs pris en charge par les métadonnées et explique comment résoudre les problèmes de liaison en modifiant ces métadonnées.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 03/09/2018
ms.openlocfilehash: f0f15647e439381c2aa291234b444d668d1b63d0
ms.sourcegitcommit: 2e5a6b8bcd1a073b54604f51538fd108e1c2a8e5
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/09/2019
ms.locfileid: "68869353"
---
# <a name="java-bindings-metadata"></a>Métadonnées de liaisons Java

_C#le code dans Xamarin. Android appelle des bibliothèques Java via des liaisons, qui sont un mécanisme qui soustrait les détails de bas niveau qui sont spécifiés dans JNI (Java Native Interface). Xamarin. Android fournit un outil qui génère ces liaisons. Cet outil permet au développeur de contrôler la façon dont une liaison est créée à l’aide de métadonnées, ce qui permet des procédures telles que la modification des espaces de noms et le changement de nom des membres. Ce document décrit le fonctionnement des métadonnées, résume les attributs pris en charge par les métadonnées et explique comment résoudre les problèmes de liaison en modifiant ces métadonnées._


## <a name="overview"></a>Présentation

Une **bibliothèque de liaisons Java** Xamarin. Android tente d’automatiser une grande partie du travail nécessaire à la liaison d’une bibliothèque Android existante avec l’aide d’un outil parfois appelé _Générateur de liaisons_. Lors de la liaison d’une bibliothèque Java, Xamarin. Android inspecte les classes Java et génère une liste de tous les packages, types et membres à lier. Cette liste d’API est stockée dans un fichier XML qui se trouve  **\{dans le répertoire du projet} \obj\Release\api.xml** pour une version Release et dans le répertoire du  **\{projet} \obj\Debug\api.xml** pour une version Debug.

![Emplacement du fichier API. xml dans le dossier obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

Le générateur de liaisons utilise le fichier **API. xml** comme indications pour générer les classes wrapper nécessaires C# . Le contenu de ce fichier XML est une variante du format de _projet open source Android_ de Google.
L’extrait de code suivant est un exemple du contenu de l' **API. xml**:

```xml
<api>
    <package name="android">
        <class abstract="false" deprecated="not deprecated" extends="java.lang.Object"
            extends-generic-aware="java.lang.Object" 
            final="true" 
            name="Manifest" 
            static="false" 
            visibility="public">
            <constructor deprecated="not deprecated" final="false"
                name="Manifest" static="false" type="android.Manifest"
                visibility="public">
            </constructor>
        </class>
...
</api>
```

Dans cet exemple, **API. xml** déclare une classe dans le `android` package nommé `Manifest` qui étend le `java.lang.Object`.

Dans de nombreux cas, une assistance humaine est nécessaire pour faire en sorte que l’API Java semble plus «.NET», ou pour corriger les problèmes qui empêchent l’assembly de liaison de se compiler. Par exemple, il peut être nécessaire de modifier les noms de packages Java en espaces de noms .NET, de renommer une classe ou de modifier le type de retour d’une méthode.

Ces modifications ne sont pas réalisées en modifiant directement **API. xml** .
Au lieu de cela, les modifications sont enregistrées dans des fichiers XML spéciaux fournis par le modèle Bibliothèque de liaisons Java. Lors de la compilation de l’assembly de liaison Xamarin. Android, le générateur de liaisons est influencé par ces fichiers de mappage lors de la création de l’assembly de liaison.

Ces fichiers de mappage XML se trouvent dans le dossier transformations du projet:

-   **Metadata. xml** &ndash; permet d’apporter des modifications à l’API finale, telles que la modification de l’espace de noms de la liaison générée. 

-   **EnumFields. xml** &ndash; contient le mappage entre les `int` constantes Java C# `enums` et. 

-   **EnumMethods,. xml** &ndash; permet de modifier les paramètres de méthode et les `int` types de retour C# `enums` des constantes Java à. 

Le fichier **Metadata. xml** est le plus importé de ces fichiers, car il permet des modifications à caractère général de la liaison, telles que:

-   Renommer des espaces de noms, des classes, des méthodes ou des champs afin qu’ils respectent les conventions .NET. 

-   Suppression des espaces de noms, des classes, des méthodes ou des champs qui ne sont pas nécessaires. 

-   Déplacement des classes vers des espaces de noms différents. 

-   L’ajout de classes de prise en charge supplémentaires pour que la conception de la liaison suive les modèles .NET Framework. 

Pour plus d’informations, passez en revue le **fichier Metadata. xml** .


## <a name="metadataxml-transform-file"></a>Fichier de transformation Metadata. Xml

Comme nous l’avons déjà appris, le fichier **Metadata. xml** est utilisé par le générateur de liaisons pour influencer la création de l’assembly de liaison.
Le format de métadonnées utilise la syntaxe [XPath](https://www.w3.org/TR/xpath/) et est quasiment identique aux *métadonnées GAPI* décrites dans Guide des [métadonnées GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) . Cette implémentation est presque une implémentation complète de XPath 1,0 et prend donc en charge les éléments de la norme 1,0. Ce fichier est un mécanisme puissant basé sur XPath permettant de modifier, d’ajouter, de masquer ou de déplacer un élément ou un attribut dans le fichier d’API. Tous les éléments de règle dans les spécifications de métadonnées incluent un attribut de chemin d’accès pour identifier le nœud auquel la règle doit être appliquée. Les règles sont appliquées dans l’ordre suivant:

* **Ajouter un nœud** &ndash; Ajoute un nœud enfant au nœud spécifié par l’attribut path.
* **attr** &ndash; Définit la valeur d’un attribut de l’élément spécifié par l’attribut path.
* **supprimer un nœud** &ndash; Supprime les nœuds correspondant à un XPath spécifié.

Voici un exemple de fichier **Metadata. xml** :

```xml
<metadata>
    <!-- Normalize the namespace for .NET -->
    <attr path="/api/package[@name='com.evernote.android.job']" 
        name="managedName">Evernote.AndroidJob</attr>

    <!-- Don't  need these packages for the Xamarin binding/public API --> 
    <remove-node path="/api/package[@name='com.evernote.android.job.v14']" />
    <remove-node path="/api/package[@name='com.evernote.android.job.v21']" />

    <!-- Change a parameter name from the generic p0 to a more meaningful one. -->
    <attr path="/api/package[@name='com.evernote.android.job']/class[@name='JobManager']/method[@name='forceApi']/parameter[@name='p0']" 
        name="name">api</attr>
</metadata>
```

La liste suivante répertorie certains des éléments XPath les plus couramment utilisés pour les API Java:

-   `interface`&ndash; Utilisé pour localiser une interface java. par exemple,. `/interface[@name='AuthListener']`

-   `class`&ndash; Utilisé pour localiser une classe. par exemple,. `/class[@name='MapView']`

-   `method`&ndash; Utilisé pour localiser une méthode sur une classe ou une interface java. par exemple,. `/class[@name='MapView']/method[@name='setTitleSource']`

-   `parameter`&ndash; Identifiez un paramètre pour une méthode. destin`/parameter[@name='p0']`



### <a name="adding-types"></a>Ajout de types

L' `add-node` élément indique au projet de liaison Xamarin. Android d’ajouter une nouvelle classe wrapper à **API. xml**. Par exemple, l’extrait de code suivant indique au générateur de liaison de créer une classe avec un constructeur et un champ unique:

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```


### <a name="removing-types"></a>Suppression de types

Il est possible de faire en sorte que le générateur de liaisons Xamarin. Android ignore un type Java et ne le lie pas. Pour ce faire, ajoutez un `remove-node` élément XML au fichier **Metadata. xml** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Attribution d’un nouveau nom aux membres

Le changement de nom des membres ne peut pas être effectué en modifiant directement le fichier **API. xml** , car Xamarin. Android requiert les noms JNI (Java Native Interface) d’origine. Par conséquent, `//class/@name` l’attribut ne peut pas être modifié; si tel est le cas, la liaison ne fonctionnera pas.

Prenons le cas où nous souhaitons renommer un type, `android.Manifest`.
Pour ce faire, nous pouvons essayer de modifier directement **API. xml** et renommer la classe comme suit:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Le générateur de liaisons crée alors le code suivant C# pour la classe wrapper:

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Notez que la classe wrapper a été renommée `NewName`, tandis que le type Java d’origine est toujours. `Manifest` Il n’est plus possible pour la classe de liaison Xamarin. Android d’accéder à des `android.Manifest`méthodes sur; la classe wrapper est liée à un type Java qui n’existe pas.

Pour modifier correctement le nom managé d’un type encapsulé (ou une méthode), il est nécessaire de définir `managedName` l’attribut comme indiqué dans cet exemple:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renommer `EventArg` des classes wrapper

Lorsque le générateur de liaisons Xamarin. Android identifie `onXXX` une méthode setter pour un _type_d’écouteur C# , un `EventArgs` événement et une sous-classe sont générés pour prendre en charge une API d’arôme .net pour le modèle d’écouteur basé sur Java. À titre d’exemple, considérez la classe Java et la méthode suivantes:

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin. Android supprime le préfixe `on` de la méthode setter et l’utilise `2DSignNextManuever` à la place comme base pour le nom `EventArgs` de la sous-classe. La sous-classe porte un nom similaire à ce qui suit:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Il ne s’agit pas C# d’un nom de classe valide. Pour corriger ce problème, l’auteur de la liaison doit `argsType` utiliser l’attribut et fournir C# un nom valide `EventArgs` pour la sous-classe:
 
```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

 

## <a name="supported-attributes"></a>Attributs pris en charge

Les sections suivantes décrivent certains des attributs de transformation des API Java.

### <a name="argstype"></a>argsType

Cet attribut est placé sur les méthodes setter pour nommer `EventArg` la sous-classe qui sera générée pour prendre en charge les écouteurs Java. Cela est décrit plus en détail ci-dessous dans la section renommer des [classes wrapper EventArg](#Renaming_EventArg_Wrapper_Classes) plus loin dans ce guide.

### <a name="eventname"></a>eventName

Spécifie un nom pour un événement. S’il est vide, il empêche la génération d’événements.
Cela est décrit plus en détail dans le titre de la section renommer des [classes wrapper EventArg](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName

Cela permet de modifier le nom d’un package, d’une classe, d’une méthode ou d’un paramètre. Par exemple, pour remplacer le nom de la classe `MyClass` `NewClassName`Java par:

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

L’exemple suivant illustre une expression XPath pour renommer la méthode `java.lang.object.toString` en: `Java.Lang.Object.NewManagedName`

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType

`managedType`est utilisé pour modifier le type de retour d’une méthode. Dans certaines situations, le générateur de liaisons déduirea de manière incorrecte le type de retour d’une méthode Java, ce qui entraînera une erreur au moment de la compilation. Une solution possible dans ce cas est de modifier le type de retour de la méthode.

Par exemple, le générateur de liaisons pense que la méthode `de.neom.neoreadersdk.resolution.compareTo()` Java doit retourner un `int`, ce qui génère le message **d’erreur CS0535: Annulation. Neom. Neoreadersdk. Resolution’n’implémente pas le membre d’interface’Java. lang. IComparable. CompareTo (Java. lang**. Object) '. L’extrait de code suivant montre comment modifier le type de paramètre de C# la méthode générée `DE.Neom.Neoreadersdk.Resolution` d’un `Java.Lang.Object`en: 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Modifie le type de retour d’une méthode. Cela ne modifie pas l’attribut de retour (étant donné que les modifications apportées aux attributs de retour peuvent entraîner des modifications incompatibles à la signature JNI). Dans l’exemple suivant, le type de retour de `append` la méthode passe de `SpannableStringBuilder` à `IAppendable` (rappelez C# -vous que ne prend pas en charge les types de retour covariants):

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>obscurci

Les outils qui brouillent les bibliothèques Java peuvent interférer avec le générateur de liaisons Xamarin. Android et C# sa capacité à générer des classes wrapper. Les caractéristiques des classes obscurcies sont les suivantes: 

* Le nom de la classe **$** comprend un, c.-à-d. **une classe $.**
* Le nom de la classe est entièrement compromis en minuscules, c.-à-d. **une classe.**

Cet extrait de code est un exemple de la façon de générer un type «non C# masqué»:

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Cet attribut peut être utilisé pour modifier le nom d’une propriété managée.

Un cas particulier d’utilisation `propertyName` de implique la situation dans laquelle une classe Java n’a qu’une méthode Getter pour un champ. Dans ce cas, le générateur de liaisons souhaiterait créer une propriété en écriture seule, ce qui est déconseillé dans .NET. L’extrait de code suivant montre comment «supprimer» les propriétés .net en définissant `propertyName` sur une chaîne vide:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Notez que les méthodes setter et getter seront toujours créées par le générateur de liaisons.

### <a name="sender"></a>sender

Spécifie le paramètre d’une méthode qui doit `sender` être le paramètre lorsque la méthode est mappée à un événement. La valeur peut être `true` ou `false`. Par exemple :

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibilité

Cet attribut est utilisé pour modifier la visibilité d’une classe, d’une méthode ou d’une propriété. Par exemple, il peut être nécessaire de promouvoir une `protected` méthode Java afin que son wrapper correspondant C# soit `public`:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields. xml et EnumMethods,. Xml

Dans certains cas, les bibliothèques Android utilisent des constantes entières pour représenter les États passés aux propriétés ou aux méthodes des bibliothèques. Dans de nombreux cas, il est utile de lier ces constantes entières aux enums dans C#. Pour faciliter ce mappage, utilisez les fichiers **EnumFields. xml** et **EnumMethods,. xml** dans votre projet de liaison. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Définition d’une énumération à l’aide de EnumFields. Xml

Le fichier **EnumFields. xml** contient le mappage entre les `int` constantes Java C# `enums`et. Prenons l’exemple suivant d’une C# énumération créée pour un ensemble de `int` constantes: 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Ici, nous avons pris la classe `SKRealReachSettings` Java et défini C# un enum `SKMeasurementUnit` appelé dans l' `Skobbler.Ngx.Map.RealReach`espace de noms. Les `field` entrées définissent le nom de la constante Java `UNIT_SECOND`(exemple), le nom de l’entrée d' `Second`énumération (exemple) et la valeur entière représentée par les `0`deux entités (exemple). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Définition des méthodes getter/Setter à l’aide de EnumMethods,. Xml

Le **fichier EnumMethods,. xml** permet de modifier les paramètres de méthode et les `int` types de retour C# `enums`des constantes Java à. En d’autres termes, il mappe la lecture et l' C# écriture des énumérations (définies dans le fichier **EnumFields. xml** ) `int` à `get` des `set` méthodes et constantes Java.

À partir `SKRealReachSettings` de l’énumération définie ci-dessus, le fichier **EnumMethods,. xml** suivant définit l’accesseur Get/Setter pour cet enum:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La première `method` ligne mappe la valeur de retour de la `getMeasurementUnit` méthode Java à `SKMeasurementUnit` l’enum. La deuxième `method` ligne mappe le premier paramètre `setMeasurementUnit` de à la même énumération.

Une fois toutes ces modifications en place, vous pouvez utiliser le code suivant dans Xamarin. Android pour définir `MeasurementUnit`: 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```


## <a name="summary"></a>Récapitulatif

Cet article a expliqué comment Xamarin. Android utilise des métadonnées pour transformer une définition d’API à partir du format *Google* *AOSP*. Après avoir couvert les modifications possibles à l’aide de *Metadata. xml*, elle a examiné les limitations rencontrées lors du changement de nom des membres et a présenté la liste des attributs XML pris en charge, décrivant le moment où chaque attribut doit être utilisé.



## <a name="related-links"></a>Liens associés

- [Utilisation de JNI](~/android/platform/java-integration/working-with-jni.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [Métadonnées GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
