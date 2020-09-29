---
title: Ressources par défaut
ms.prod: xamarin
ms.assetid: 762572F0-173A-D994-0510-8F36BEF3D487
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 03/13/2018
ms.openlocfilehash: 1b25a0f7e39b84582491a2a80536516a42a89519
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91455480"
---
# <a name="default-resources"></a>Ressources par défaut

Les ressources par défaut sont des éléments qui ne sont pas spécifiques à un appareil ou un facteur de forme particulier, et sont par conséquent le choix par défaut par le système d’exploitation Android si aucune ressource spécifique n’est trouvée. Par conséquent, il s’agit du type de ressource le plus courant à créer. Ils sont organisés dans des sous-répertoires du répertoire **Resources** en fonction de leur type de ressource :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

![Fichiers de ressources par défaut](default-resources-images/01-resource-files-vs.png)

Dans l’image ci-dessus, le projet a des valeurs par défaut pour les ressources, les mises en page et les valeurs pouvant être dessinées (fichiers XML qui contiennent des valeurs simples).

La liste complète des types de ressources est fournie ci-dessous :

- **animateur** &ndash; Fichiers XML qui décrivent les animations de propriété.
   Les animations de propriété ont été introduites dans le niveau d’API 11 (Android 3,0) et fournissent l’animation des propriétés sur un objet. Les animations de propriétés sont un moyen plus souple et plus puissant de décrire des animations sur tout type d’objet.

- **anim** le &ndash; Fichiers XML qui décrivent les animations d' *interpolation* . Les animations d’interpolation sont une série d’instructions d’animation permettant d’effectuer des transformations sur le contenu d’un objet de vue, ou de faire pivoter une image ou de augmenter la taille du texte. Les animations d’interpolation sont limitées uniquement aux objets de vue.

- **couleur** &ndash; Fichiers XML qui décrivent une liste des États des couleurs. Pour comprendre les listes d’état des couleurs, envisagez un widget d’interface utilisateur tel qu’un bouton.
   Il peut avoir des États différents, par exemple enfoncé ou désactivé, et le bouton peut changer de couleur à chaque modification de l’État. La liste est exprimée dans une liste d’États.

- **dessinable** &ndash; Les ressources pouvant être dessinées sont un concept général pour les graphiques qui peuvent être compilés dans l’application, puis accessibles par des appels d’API ou référencés par d’autres ressources XML.
   Voici quelques exemples de drawables : fichiers bitmap (. png,. gif,. jpg), images bitmap redimensionnables spéciales appelées [neuf correctifs](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listes d’États, formes génériques définies dans XML, etc.

- **disposition** &ndash; Fichiers XML qui décrivent une disposition d’interface utilisateur, telle qu’une activité ou une ligne d’une liste.

- **menu** &ndash; Fichiers XML qui décrivent des menus d’application tels que des *menus d’options*, des *menus contextuels*et des *sous-menus*. Pour obtenir un exemple de menus, consultez la [démonstration du menu contextuel](/samples/xamarin/monodroid-samples/popupmenudemo) ou l’exemple de [contrôles standard](/samples/xamarin/mobile-samples/standardcontrols/) .

- **brut** &ndash; Fichiers arbitraires enregistrés sous leur forme binaire brute. Ces fichiers sont compilés dans une application Android dans un format binaire.

- **valeurs** &ndash; Fichiers XML qui contiennent des valeurs simples. Un fichier XML dans le répertoire values ne définit pas une seule ressource, mais peut à la place définir plusieurs ressources. Par exemple, un fichier XML peut contenir une liste de valeurs de chaîne, tandis qu’un autre fichier XML peut contenir une liste de valeurs de couleur.

- **xml** &ndash; fichiers XML XML qui fonctionnent de la même façon que les fichiers de configuration .net. Il s’agit de données XML arbitraires qui peuvent être lues au moment de l’exécution par l’application.

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

![Fichiers de ressources par défaut](default-resources-images/01-resource-files-xs.png)

Dans l’image ci-dessus, le projet a des valeurs par défaut pour les ressources, les mises en page et les valeurs pouvant être dessinées (fichiers XML qui contiennent des valeurs simples).

La liste complète des types de ressources est fournie ci-dessous :

- **animateur** &ndash; Fichiers XML qui décrivent les animations de propriété.
   Les animations de propriété ont été introduites dans le niveau d’API 11 (Android 3,0) et fournissent l’animation des propriétés sur un objet. Les animations de propriétés sont un moyen plus souple et plus puissant de décrire des animations sur tout type d’objet.

- **anim** le &ndash; Fichiers XML qui décrivent les animations d' *interpolation* . Les animations d’interpolation sont une série d’instructions d’animation permettant d’effectuer des transformations sur le contenu d’un objet de vue, ou de faire pivoter une image ou de augmenter la taille du texte. Les animations d’interpolation sont limitées uniquement aux objets de vue.

- **couleur** &ndash; Fichiers XML qui décrivent une liste des États des couleurs. Pour comprendre les listes d’état des couleurs, envisagez un widget d’interface utilisateur tel qu’un bouton.
   Elle peut avoir des États différents, par exemple activé ou désactivé, et le bouton peut changer de couleur à chaque modification de l’État. La liste est exprimée dans une liste d’États.

- **police** &ndash; À partir de l’API de niveau 26, il est possible d’incorporer des polices en tant que ressource dans une application Android. La bibliothèque de prise en charge 26 va rétroporter les polices au niveau d’API 14. L’incorporation de polices permet aux applications de charger des polices personnalisées directement à partir de dispositions XML sans avoir à les importer en tant que ressources avant de les utiliser.

- **mipmap** &ndash; Les ressources pouvant être dessinées sont un concept général pour les graphiques qui peuvent être compilés dans l’application, puis accessibles par des appels d’API ou référencés par d’autres ressources XML.
   Voici quelques exemples de drawables : fichiers bitmap (. png,. gif,. jpg), images bitmap redimensionnables spéciales appelées [neuf correctifs](https://developer.android.com/guide/topics/graphics/2d-graphics.html#nine-patch), listes d’États, formes génériques définies dans XML, etc.

- **disposition** &ndash; Fichiers XML qui décrivent une disposition d’interface utilisateur, telle qu’une activité ou une ligne d’une liste.

- **menu** &ndash; Fichiers XML qui décrivent des menus d’application tels que des *menus d’options*, des *menus contextuels*et des *sous-menus*. Pour obtenir un exemple de menus, consultez la [démonstration du menu contextuel](/samples/xamarin/monodroid-samples/popupmenudemo) ou l’exemple de [contrôles standard](/samples/xamarin/mobile-samples/standardcontrols/) .

- **brut** &ndash; Fichiers arbitraires enregistrés sous leur forme binaire brute. Ces fichiers sont compilés dans une application Android dans un format binaire.

- **valeurs** &ndash; Fichiers XML qui contiennent des valeurs simples. Un fichier XML dans le répertoire values ne définit pas une seule ressource, mais peut à la place définir plusieurs ressources. Par exemple, un fichier XML peut contenir une liste de valeurs de chaîne, tandis qu’un autre fichier XML peut contenir une liste de valeurs de couleur.

- **xml** &ndash; fichiers XML XML qui fonctionnent de la même façon que les fichiers de configuration .net. Il s’agit de données XML arbitraires qui peuvent être lues au moment de l’exécution par l’application.

-----