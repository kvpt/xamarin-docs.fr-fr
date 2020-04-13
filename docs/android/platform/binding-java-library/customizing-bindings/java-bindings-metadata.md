---
title: Métadonnées de liaisons Java
description: Le code Cmd dans Xamarin.Android appelle les bibliothèques Java par le biais de liaisons, qui sont un mécanisme qui résume les détails de bas niveau qui sont spécifiés dans Java Native Interface (JNI). Xamarin.Android fournit un outil qui génère ces liaisons. Cet outillage permet au développeur de contrôler la façon dont une liaison est créée en utilisant des métadonnées, ce qui permet des procédures telles que la modification des espaces nominatifs et le changement de nom des membres. Ce document traite du fonctionnement des métadonnées, résume les attributs que les métadonnées supportent et explique comment résoudre les problèmes contraignants en modifiant ces métadonnées.
ms.prod: xamarin
ms.assetid: 27CB3C16-33F3-F580-E2C0-968005A7E02E
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/09/2018
ms.openlocfilehash: 25a5d79084f7caa78eec4011c047bd19a63ef748
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "75487787"
---
# <a name="java-bindings-metadata"></a>Métadonnées de liaisons Java

_Le code Cmd dans Xamarin.Android appelle les bibliothèques Java par le biais de liaisons, qui sont un mécanisme qui résume les détails de bas niveau qui sont spécifiés dans Java Native Interface (JNI). Xamarin.Android fournit un outil qui génère ces liaisons. Cet outillage permet au développeur de contrôler la façon dont une liaison est créée en utilisant des métadonnées, ce qui permet des procédures telles que la modification des espaces nominatifs et le changement de nom des membres. Ce document traite du fonctionnement des métadonnées, résume les attributs que les métadonnées supportent et explique comment résoudre les problèmes contraignants en modifiant ces métadonnées._

## <a name="overview"></a>Vue d’ensemble

Une bibliothèque de **liaison Java** Xamarin.Android tente d’automatiser une grande partie du travail nécessaire pour lier une bibliothèque Android existante à l’aide d’un outil parfois connu sous le nom _de générateur de liaisons_. Lors de la liaison d’une bibliothèque Java, Xamarin.Android inspectera les classes Java et générera une liste de tous les paquets, types et membres qui doivent être liés. Cette liste d’API est stockée dans un fichier XML qui peut être trouvé à ** \{l’annuaire du projet pour** une build **RELEASE** et à ** \{l’annuaire du projet .obj-Debug-api.xml** pour une version **DEBUG.**

![Emplacement du fichier api.xml dans le dossier obj/Debug](java-bindings-metadata-images/java-bindings-metadata-01.png)

Le générateur de liaisons utilisera le fichier **api.xml** comme ligne directrice pour générer les classes d’emballageS CMD nécessaires. Le contenu de ce fichier XML est une variante du format _Android Open Source Project_ de Google.
L’extrait suivant est un exemple du contenu de **api.xml**:

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

Dans cet exemple, **api.xml** déclare `android` une `Manifest` classe dans `java.lang.Object`le paquet nommé qui étend le .

Dans de nombreux cas, l’assistance humaine est nécessaire pour que l’API Java se sente plus ".NET like" ou pour corriger les problèmes qui empêchent l’assemblage contraignant de compiler. Par exemple, il peut être nécessaire de changer les noms de paquets Java pour .NET namespaces, renommer une classe, ou changer le type de retour d’une méthode.

Ces modifications ne sont pas réalisées en modifiant directement **api.xml.**
Au lieu de cela, les modifications sont enregistrées dans des fichiers XML spéciaux qui sont fournis par le modèle Java Binding Library. Lors de la compilation de l’assemblage de liaison Xamarin.Android, le générateur de liaisons sera influencé par ces fichiers de cartographie lors de la création de l’assemblage de liaison

Ces fichiers de cartographie XML peuvent être trouvés dans le dossier **Transforms** du projet :

- **MetaData.xml** &ndash; Permet d’apporter des modifications à l’API finale, comme la modification de l’espace nom de la liaison générée. 

- **EnumFields.xml** &ndash; Contient la `int` cartographie entre les `enums` constantes Java et C . 

- **EnumMethods.xml** &ndash; Permet de changer les paramètres `int` de la `enums` méthode et les types de retour des constantes Java à C . 

Le fichier **MetaData.xml** est le plus important de ces fichiers car il permet des modifications générales à la liaison telles que:

- Renommer les espaces de noms, les classes, les méthodes ou les champs afin qu’ils suivent les conventions .NET. 

- Suppression des espaces de noms, des classes, des méthodes ou des champs qui ne sont pas nécessaires. 

- Déplacer les classes vers différents espaces de noms. 

- Ajout de classes de soutien supplémentaires pour rendre la conception de la liaison suivre .NET modèles de cadre. 

Passons à discuter **de Metadata.xml** plus en détail.

## <a name="metadataxml-transform-file"></a>Fichier de transformation de Métaadata.xml

Comme nous l’avons déjà appris, le fichier **Metadata.xml** est utilisé par le générateur de liaisons pour influencer la création de l’assemblage contraignant.
Le format de métadonnées utilise la syntaxe [XPath](https://www.w3.org/TR/xpath/) et est presque identique aux *métadonnées GAPI décrites* dans le guide [GAPI Metadata.](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata) Cette implémentation est presque une mise en œuvre complète de XPath 1.0 et prend ainsi en charge les éléments de la norme 1.0. Ce fichier est un puissant mécanisme basé sur XPath pour modifier, ajouter, masquer ou déplacer n’importe quel élément ou attribut dans le fichier API. Tous les éléments de la règle dans la spécification des métadonnées comprennent un attribut de voie pour identifier le nœud auquel la règle doit être appliquée. Les règles sont appliquées dans l’ordre suivant :

- **add-node** &ndash; Annexe un nœud enfant au nœud spécifié par l’attribut du chemin.
- **attr** &ndash; définit la valeur d’un attribut de l’élément spécifié par l’attribut du chemin.
- **supprimer-nœud** &ndash; Supprime les nœuds correspondant à un XPath spécifié.

Ce qui suit est un exemple d’un fichier **Metadata.xml:**

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

Les listes suivantes quelques-uns des éléments XPath les plus couramment utilisés pour l’API Java:

- `interface`&ndash; Utilisé pour localiser une interface Java. Exemple : `/interface[@name='AuthListener']`.

- `class`&ndash; Utilisé pour localiser une classe . Exemple : `/class[@name='MapView']`.

- `method`&ndash; Utilisé pour localiser une méthode sur une classe ou une interface Java. Exemple : `/class[@name='MapView']/method[@name='setTitleSource']`.

- `parameter`&ndash; Identifier un paramètre pour une méthode. Exemple : `/parameter[@name='p0']`

### <a name="adding-types"></a>Ajout de types

L’élément `add-node` dira au projet de liaison Xamarin.Android d’ajouter une nouvelle classe d’emballage à **api.xml**. Par exemple, l’extrait suivant dirigera le générateur de liaison pour créer une classe avec un constructeur et un seul champ :

```xml
<add-node path="/api/package[@name='org.alljoyn.bus']">
    <class abstract="false" deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="true" visibility="public" extends="java.lang.Object">
        <constructor deprecated="not deprecated" final="false" name="AuthListener.AuthRequest" static="false" type="org.alljoyn.bus.AuthListener.AuthRequest" visibility="public" />
        <field name="p0" type="org.alljoyn.bus.AuthListener.Credentials" />
    </class>
</add-node>
```

### <a name="removing-types"></a>Suppression des types

Il est possible d’instruire le générateur de liaisons Xamarin.Android pour ignorer un type Java et ne pas le lier. Ceci est fait `remove-node` en ajoutant un élément XML au fichier **métadonnées.xml** :

```xml
<remove-node path="/api/package[@name='{package_name}']/class[@name='{name}']" />
```

### <a name="renaming-members"></a>Renommer les membres

Le changement de nom des membres ne peut se faire en éditant directement le fichier **api.xml** parce que Xamarin.Android nécessite les noms originaux de Java Native Interface (JNI). Par conséquent, l’attribut `//class/@name` ne peut pas être modifié; si c’est le cas, la liaison ne fonctionnera pas.

Considérez le cas où nous voulons `android.Manifest`renommer un type, .
Pour ce faire, nous pourrions essayer d’éditer directement **api.xml** et renommer la classe comme ça:

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="name">NewName</attr>
```

Il en résultera la création du code Cmd suivant pour la classe d’emballage :

```csharp
[Register ("android/NewName")]
public class NewName : Java.Lang.Object { ... }
```

Notez que la classe d’emballage a été rebaptisée à `NewName`, tandis que le type Java d’origine est encore `Manifest`. Il n’est plus possible pour la classe de liaison `android.Manifest`Xamarin.Android d’accéder à toutes les méthodes sur ; la classe d’emballage est liée à un type Java inexistant.

Pour changer correctement le nom géré d’un type (ou `managedName` méthode enveloppé), il est nécessaire de définir l’attribut tel que indiqué dans cet exemple :

```xml
<attr path="/api/package[@name='android']/class[@name='Manifest']" 
    name="managedName">NewName</attr>
```

<a name="Renaming_EventArg_Wrapper_Classes" />

#### <a name="renaming-eventarg-wrapper-classes"></a>Renommer `EventArg` les classes d’emballage

Lorsque le générateur de liaison Xamarin.Android identifie une `onXXX` méthode de setter pour un type _d’auditeur,_ un événement et `EventArgs` une sous-classe de C seront générés pour prendre en charge une API aromatisée .NET pour le modèle d’auditeur basé à Java. À titre d’exemple, considérez la classe et la méthode Java suivantes :

```xml
com.someapp.android.mpa.guidance.NavigationManager.on2DSignNextManuever(NextManueverListener listener);
```

Xamarin.Android va laisser `on` tomber le préfixe `2DSignNextManuever` de la méthode setter `EventArgs` et plutôt utiliser comme base pour le nom de la sous-classe. La sous-classe sera nommée quelque chose de similaire à:

```csharp
NavigationManager.2DSignNextManueverEventArgs
```

Ce n’est pas un nom légal de classe C. Pour corriger ce problème, l’auteur contraignant doit utiliser l’attribut `argsType` et fournir un nom C en valide pour la `EventArgs` sous-classe :

```xml
<attr path="/api/package[@name='com.someapp.android.mpa.guidance']/
    interface[@name='NavigationManager.Listener']/
    method[@name='on2DSignNextManeuver']" 
    name="argsType">NavigationManager.TwoDSignNextManueverEventArgs</attr>
```

## <a name="supported-attributes"></a>Attributs pris en charge

Les sections suivantes décrivent certains des attributs pour transformer les API Java.

### <a name="argstype"></a>argsType argsType

Cet attribut est placé sur les `EventArg` méthodes de setter pour nommer la sous-classe qui sera générée pour soutenir les auditeurs java. Ceci est décrit plus en détail ci-dessous dans la section [Renaming EventArg Wrapper Classes](#Renaming_EventArg_Wrapper_Classes) plus tard dans ce guide.

### <a name="eventname"></a>eventName

Précise un nom pour un événement. S’il est vide, il inhibe la génération d’événements.
Ceci est décrit plus en détail dans le titre de section [Renaming EventArg Wrapper Classes](#Renaming_EventArg_Wrapper_Classes).

### <a name="managedname"></a>managedName (en)

Ceci est utilisé pour changer le nom d’un paquet, classe, méthode, ou paramètre. Par exemple pour changer le `MyClass` nom `NewClassName`de la classe Java pour :

```xml
<attr path="/api/package[@name='com.my.application']/class[@name='MyClass']" 
    name="managedName">NewClassName</attr>
```

L’exemple suivant illustre une expression XPath `java.lang.object.toString` pour `Java.Lang.Object.NewManagedName`renommer la méthode pour :

```xml
<attr path="/api/package[@name='java.lang']/class[@name='Object']/method[@name='toString']" 
    name="managedName">NewMethodName</attr>
```

### <a name="managedtype"></a>managedType (en)

`managedType`est utilisé pour changer le type de retour d’une méthode. Dans certaines situations, le générateur de liaisons infère à tort le type de retour d’une méthode Java, ce qui entraînera une erreur de temps de compilation. Une solution possible dans cette situation est de changer le type de retour de la méthode.

Par exemple, le générateur de liaisons estime que la méthode `de.neom.neoreadersdk.resolution.compareTo()` Java devrait retourner un `int` et prendre `Object` comme paramètres, ce qui entraîne le message **d’erreur Erreur CS0535: 'DE. Neom.Neoreadersdk.Resolution' ne met pas en œuvre le membre de l’interface 'Java.Lang.IComparable.CompareTo (Java.Lang.Object)'**. L’extrait suivant montre comment modifier le type de la méthode C générée `DE.Neom.Neoreadersdk.Resolution` du `Java.Lang.Object`premier paramètre d’un à l’autre : 

```xml
<attr path="/api/package[@name='de.neom.neoreadersdk']/
    class[@name='Resolution']/
    method[@name='compareTo' and count(parameter)=1 and
    parameter[1][@type='de.neom.neoreadersdk.Resolution']]/
    parameter[1]" name="managedType">Java.Lang.Object</attr> 
```

### <a name="managedreturn"></a>managedReturn

Modifie le type de retour d’une méthode. Cela ne modifie pas l’attribut de retour (car les modifications apportées aux attributs de retour peuvent entraîner des modifications incompatibles de la signature de l’INN). Dans l’exemple suivant, le `append` type de `SpannableStringBuilder` `IAppendable` retour de la méthode est changé de à (rappel que C n’est pas en charge les types de retour covariant) :

```xml
<attr path="/api/package[@name='android.text']/
    class[@name='SpannableStringBuilder']/
    method[@name='append']" 
    name="managedReturn">Java.Lang.IAppendable</attr>
```

### <a name="obfuscated"></a>Obfuscated

Les outils qui obscurcient les bibliothèques Java peuvent interférer avec le générateur de liaison Xamarin.Android et sa capacité à générer des classes d’emballage C. Les caractéristiques des classes obfusquées comprennent : 

- Le nom de **$** la classe comprend un , c’est-à-dire **une classe$.**
- Le nom de la classe est entièrement compromis des caractères minuscules, c’est-à-dire **a.class**

Cet extrait est un exemple de la façon de générer un type « non obfusqué » de CMD :

```xml
<attr path="/api/package[@name='{package_name}']/class[@name='{name}']" 
    name="obfuscated">false</attr>
```

### <a name="propertyname"></a>propertyName

Cet attribut peut être utilisé pour changer le nom d’une propriété gérée.

Un cas spécialisé `propertyName` d’utilisation implique la situation où une classe Java n’a qu’une méthode d’adier pour un champ. Dans cette situation, le générateur de liaison voudrait créer une propriété d’écriture seulement, quelque chose qui est découragé dans .NET. L’extrait suivant montre comment «supprimer» les propriétés `propertyName` .NET en fixant la chaîne vide:

```xml
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='setResourceDescriptor' 
    and count(parameter)=1 
    and parameter[1][@type='java.lang.String']]" 
    name="propertyName"></attr>
<attr path="/api/package[@name='org.java_websocket.handshake']/class[@name='HandshakeImpl1Client']/method[@name='getResourceDescriptor' 
    and count(parameter)=0]" 
    name="propertyName"></attr>
```

Notez que les méthodes de setter et d’adure seront toujours créées par le générateur de liaisons.

### <a name="sender"></a>expéditeur

Précise quel paramètre d’une `sender` méthode doit être le paramètre lorsque la méthode est cartographiée pour un événement. La valeur peut être `true` ou `false`. Par exemple :

```xml
<attr path="/api/package[@name='android.app']/
    interface[@name='TimePickerDialog.OnTimeSetListener']/
    method[@name='onTimeSet']/
    parameter[@name='view']" 
    name="sender">true</ attr>
```

### <a name="visibility"></a>visibility

Cet attribut est utilisé pour modifier la visibilité d’une classe, d’une méthode ou d’une propriété. Par exemple, il peut être `protected` nécessaire de promouvoir une méthode Java `public`afin qu’il soit correspondant c ' emballage est:

```xml
<!-- Change the visibility of a class -->
<attr path="/api/package[@name='namespace']/class[@name='ClassName']" name="visibility">public</attr>

<!-- Change the visibility of a method --> 
<attr path="/api/package[@name='namespace']/class[@name='ClassName']/method[@name='MethodName']" name="visibility">public</attr>
```

## <a name="enumfieldsxml-and-enummethodsxml"></a>EnumFields.xml et EnumMethods.xml

Il ya des cas où les bibliothèques Android utilisent des constantes integer pour représenter les États qui sont transmis aux propriétés ou aux méthodes des bibliothèques. Dans de nombreux cas, il est utile de lier ces constantes integer à enums dans C . Pour faciliter cette cartographie, utilisez les fichiers **EnumFields.xml** et **EnumMethods.xml** dans votre projet de liaison. 

### <a name="defining-an-enum-using-enumfieldsxml"></a>Définir un Enum à l’aide d’EnumFields.xml

Le fichier **EnumFields.xml** contient `int` la cartographie entre `enums`les constantes Java et C . Prenons l’exemple suivant d’une création d’enum `int` CMD pour un ensemble de constantes : 

```xml 
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit">
    <field jni-name="UNIT_SECOND" clr-name="Second" value="0" />
    <field jni-name="UNIT_METER" clr-name="Meter" value="1" />
    <field jni-name="UNIT_MILIWATT_HOURS" clr-name="MilliwattHour" value="2" />
</mapping>
```

Ici, nous avons `SKRealReachSettings` pris la classe Java `SKMeasurementUnit` et défini `Skobbler.Ngx.Map.RealReach`un enum C appelé dans l’espace de nom . Les `field` entrées définissent le nom de `UNIT_SECOND`la constante Java (exemple), `Second`le nom de l’entrée enum (exemple), et la valeur integer représentée par les deux entités (exemple `0`). 

### <a name="defining-gettersetter-methods-using-enummethodsxml"></a>Définir les méthodes Getter/Setter à l’aide d’EnumMethods.xml

Le fichier **EnumMethods.xml** permet de modifier les `int` paramètres de `enums`la méthode et les types de retour des constantes Java à C . En d’autres termes, il cartographie la lecture et l’écriture de enums C (défini `set` dans le fichier **EnumFields.xml)** à Java `int` constante `get` et les méthodes.

Compte `SKRealReachSettings` tenu de l’enum défini ci-dessus, le fichier **Suivant EnumMethods.xml** définirait l’getter/setter pour cet enum:

```xml
<mapping jni-class="com/skobbler/ngx/map/realreach/SKRealReachSettings">
    <method jni-name="getMeasurementUnit" parameter="return" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
    <method jni-name="setMeasurementUnit" parameter="measurementUnit" clr-enum-type="Skobbler.Ngx.Map.RealReach.SKMeasurementUnit" />
</mapping>
```

La `method` première ligne cartographie la `getMeasurementUnit` valeur de `SKMeasurementUnit` retour de la méthode Java à l’enum. La `method` deuxième ligne cartographie le `setMeasurementUnit` premier paramètre du même enum.

Avec tous ces changements en place, vous pouvez utiliser le code `MeasurementUnit`de suivi dans Xamarin.Android pour définir le : 

```csharp
realReachSettings.MeasurementUnit = SKMeasurementUnit.Second;
```

## <a name="summary"></a>Récapitulatif

Cet article a discuté de la façon dont Xamarin.Android utilise des métadonnées pour transformer une définition API à partir du *format AOSP* *de Google* . Après avoir couvert les modifications qui sont possibles à *l’aide de Metadata.xml*, il a examiné les limitations rencontrées lors du changement de nom des membres et il a présenté la liste des attributs XML pris en charge, décrivant quand chaque attribut doit être utilisé.

## <a name="related-links"></a>Liens connexes

- [Travailler avec JNI](~/android/platform/java-integration/working-with-jni.md)
- [Liaison d’une bibliothèque Java](~/android/platform/binding-java-library/index.md)
- [Métadonnées GAPI](https://www.mono-project.com/docs/gui/gtksharp/gapi/#metadata)
