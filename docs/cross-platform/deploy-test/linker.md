---
title: Configuration personnalisée de l’éditeur de liens
description: Ce document décrit un fichier XML que vous pouvez utiliser pour configurer l’éditeur de liens, afin de vous assurer explicitement que le code nécessaire n’est pas supprimé de l’application liée.
ms.prod: xamarin
ms.assetid: F8A99E3F-2197-4399-AC81-F1DBAB5729C9
author: conceptdev
ms.author: crdun
ms.date: 03/22/2017
ms.openlocfilehash: 230fe0f168b5718c2bc91cff6dbdc078b0e6834d
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70765933"
---
# <a name="custom-linker-configuration"></a>Configuration personnalisée de l’éditeur de liens

Si l’ensemble d’options par défaut ne suffit pas, vous pouvez piloter le processus de liaison avec un fichier XML décrivant ce que vous voulez faire faire à l’éditeur de liens.

Vous pouvez fournir à l’éditeur de liens des définitions supplémentaires pour garantir que le type, les méthodes et/ou les champs ne sont pas éliminés de votre application. Dans votre propre code, le meilleur moyen consiste à utiliser l’attribut personnalisé `[Preserve]`, tel que décrit dans les guides [Liaison sur iOS](~/ios/deploy-test/linker.md) et [Liaison sur Android](~/android/deploy-test/linker.md).
Toutefois si vous avez besoin de définitions issues du Kit de développement logiciel (SDK) ou des assemblys du produit, l’utilisation d’un fichier XML est sans doute la meilleure solution (contrairement à l’ajout de code qui permettra d’assurer que l’éditeur de liens n’éliminera pas ce dont vous avez besoin).

Pour ce faire, définissez un fichier XML avec l’élément de niveau supérieur `<linker>` qui contient les nœuds *assembly* contenant à leur tour les nœuds *type*, contenant pour leur part les nœuds *méthode* et *champ*.

Une fois que vous avez ce fichier de description de l’éditeur de liens, ajoutez-le à votre projet et :

- **Pour Android** : définissez  **Action de génération** sur **LinkDescription**
- **Pour iOS** : définissez **Action de génération** sur **LinkDescription**

L’exemple suivant montre à quoi ressemble le fichier XML :

```xml
<linker>
        <assembly fullname="mscorlib">
                <type fullname="System.Environment">
                        <field name="mono_corlib_version" />
                        <method name="get_StackTrace" />
                </type>
        </assembly>
        <assembly fullname="My.Own.Assembly">
                <type fullname="Foo" preserve="fields">
                        <method name=".ctor" />
                </type>
                <type fullname="Bar">
                        <method signature="System.Void .ctor(System.String)" />
                        <field signature="System.String _blah" />
                </type>
                <namespace fullname="My.Own.Namespace" />
                <type fullname="My.Other*" />
        </assembly>
</linker>
```

Dans l’exemple ci-dessus, l’éditeur de liens lit et applique les instructions sur les assemblys `mscorlib.dll` (livrés avec Mono pour Android) et `My.Own.Assembly` (code utilisateur).

La première section, pour `mscorlib.dll`, permet de garantir que le type `System.Environment` conserve son champ nommé `mono_corlib_version` et sa méthode `get_StackTrace`.
Notez que les noms de la méthode getter et/ou setter doivent être utilisés tandis que l’éditeur de liens fonctionne sur le langage IL sans comprendre des propriétés de C#.

La seconde section, pour `My.Own.Assembly.dll`, permet de garantir que le type `Foo` conserve tous ses champs (par ex. l’attribut `preserve="fields"`) et tous ses constructeurs (par ex. toutes les méthodes nommées `.ctor` dans IL). Le type `Bar` conserve des signatures spécifiques (pas de noms) pour un constructeur (qui accepte un paramètre de chaîne unique) et pour un champ de chaîne spécifique `_blah`.
L’espace de noms `My.Own.Namespace` permet de conserver tous les types qu’il contient.
Enfin, n’importe quel type dont le nom complet (y compris l’espace de noms) correspond au modèle de caractère générique « My.Other\* » conserve tous ses champs et toutes ses méthodes. Le caractère générique `*` peut être inclus plusieurs fois dans un modèle de « nom complet de type ».

## <a name="related-links"></a>Liens associés

- [Liaison sur iOS](~/ios/deploy-test/linker.md)
- [Liaison sur Android](~/android/deploy-test/linker.md)
