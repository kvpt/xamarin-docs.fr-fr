---
title: Contrôles Android (widgets)
description: Blocs de construction pour la création d’interfaces utilisateur Xamarin. Android
ms.prod: xamarin
ms.assetid: B7A82166-B920-4672-B7A2-20DD5E0B5AEF
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/29/2018
ms.openlocfilehash: 25e33ee2d77501b3913a3598ef7855714f0b9bc1
ms.sourcegitcommit: 52fb214c0e0243587d4e9ad9306b75e92a8cc8b7
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 02/01/2020
ms.locfileid: "78293051"
---
# <a name="xamarinandroid-controls-widgets"></a>Contrôles Xamarin. Android (widgets)

Xamarin. Android expose tous les contrôles d’interface utilisateur natif (widgets) fournis par Android. Ces contrôles peuvent être facilement ajoutés aux applications Xamarin. Android à l’aide de la Android Designer ou par programmation via des fichiers de disposition XML. Quelle que soit la méthode choisie, Xamarin. Android expose toutes les propriétés et méthodes de l’objet d’interface C#utilisateur dans. Les sections suivantes présentent les contrôles d’interface utilisateur Android les plus courants et expliquent comment les incorporer dans des applications Xamarin. Android.

## <a name="action-bar"></a>[Barre d'action](~/android/user-interface/controls/action-bar.md) 

`ActionBar` est une barre d’outils qui affiche le titre de l’activité, les interfaces de navigation et d’autres éléments interactifs. En règle générale, la barre d’action apparaît en haut de la fenêtre d’une activité.

![Exemple de barre](images/action-bar.png)

## <a name="auto-complete"></a>[Saisie semi-automatique](~/android/user-interface/controls/auto-complete.md)

`AutoCompleteTextView` est un élément d’affichage de texte modifiable qui affiche automatiquement les suggestions de saisie semi-automatique pendant que l’utilisateur tape. La liste de suggestions s’affiche dans un menu déroulant dans lequel l’utilisateur peut choisir un élément pour remplacer le contenu de la zone d’édition par.

![Exemple de saisie semi-automatique](images/auto-complete.png)

## <a name="buttons"></a>[Boutons](~/android/user-interface/controls/buttons/index.md)

Les boutons sont des éléments d’interface utilisateur que l’utilisateur clique pour exécuter une action.

![Exemples de boutons](images/buttons.png)

## <a name="calendar"></a>[Calendrier](~/android/user-interface/controls/calendar.md)

La classe `Calendar` est utilisée pour convertir une instance spécifique dans le temps (une valeur de milliseconde qui est décalée de l’époque) en valeurs telles que l’année, le mois, l’heure, le jour du mois et la date de la semaine suivante.
`Calendar` prend en charge de nombreuses options d’interaction avec les données de calendrier, notamment la possibilité de lire et d’écrire des événements, des participants et des rappels. En utilisant le fournisseur de calendrier dans votre application, les données que vous ajoutez à l’aide de l’API s’affichent dans l’application de calendrier intégrée qui est fournie avec Android.

![Exemple de calendrier](images/calendar.png)

## <a name="cardview"></a>[CardView](~/android/user-interface/controls/card-view.md)

`CardView` est un composant d’interface utilisateur qui présente le contenu du texte et de l’image dans les affichages qui ressemblent aux cartes. `CardView` est implémenté en tant que widget `FrameLayout` avec des angles arrondis et une ombre. En règle générale, un `CardView` est utilisé pour présenter un élément de ligne unique dans un groupe d’affichage `ListView` ou `GridView`.

![Exemple de vue carte](images/cardview.png)

## <a name="edit-text"></a>[Modifier le texte](~/android/user-interface/controls/edit-text.md)

`EditText` est un élément d’interface utilisateur qui permet d’entrer et de modifier du texte.

![Exemple de modification de texte](images/edit-text.png)

## <a name="gallery"></a>[Galerie](~/android/user-interface/controls/gallery.md)

`Gallery` est un widget de disposition qui est utilisé pour afficher des éléments dans une liste de défilement horizontale ; Il positionne la sélection actuelle au centre de la vue.

![Galerie d’exemples](images/gallery.png)

## <a name="navigation-bar"></a>[Barre de navigation](~/android/user-interface/controls/navigation-bar.md)

La *barre de navigation* fournit des contrôles de navigation sur les appareils qui n’incluent pas de boutons matériels pour la **page de démarrage**, l' **arrière-plan**et le **menu**.

![Exemple de barre de navigation](images/navigation-bar.png)

## <a name="pickers"></a>[Sélecteurs](~/android/user-interface/controls/pickers/index.md)

Les *sélecteurs* sont des éléments d’interface utilisateur qui permettent à l’utilisateur de choisir une date ou une heure à l’aide des boîtes de dialogue fournies par Android.

![Exemple de sélecteur](images/picker.png)

## <a name="popup-menu"></a>[Menu contextuel](~/android/user-interface/controls/popup-menu.md)

`PopupMenu` est utilisé pour afficher les menus contextuels attachés à une vue particulière.

![Exemple de menu contextuel](images/popup-menu.png)

## <a name="ratingbar"></a>[RatingBar](~/android/user-interface/controls/ratingbar.md)

Un `RatingBar` est un élément d’interface utilisateur qui affiche une évaluation dans étoiles.

![Exemple de RatingBar](ratingbar-images/01-ratingbar.png)

## <a name="spinner"></a>[Spinner](~/android/user-interface/controls/spinner.md)

`Spinner` est un élément d’interface utilisateur qui fournit un moyen rapide de sélectionner une valeur dans un ensemble. Elle est similaire à une liste déroulante. 

![Exemple de compteur](images/spinner.png)

## <a name="switch"></a>[Switch](~/android/user-interface/controls/switch.md)

`Switch` est un élément d’interface utilisateur qui permet à un utilisateur de basculer entre deux États, comme ON ou OFF. La valeur par défaut de `Switch` est OFF.

![Exemple de commutateur](images/switch.png)

## <a name="textureview"></a>[TextureView](~/android/user-interface/controls/texture-view.md)

`TextureView` est une vue qui utilise le rendu 2D avec accélération matérielle pour permettre l’affichage d’un flux de contenu vidéo ou OpenGL.

![Exemple d’affichage de texture](images/texture-view.png)

## <a name="toolbar"></a>[ToolBar](~/android/user-interface/controls/tool-bar/index.md)

Le widget `Toolbar` (introduit dans Android 5,0 Lollipop) peut être considéré comme une généralisation de l’interface de la barre d’action &ndash; il est destiné à remplacer la barre d’action. La `Toolbar` peut être utilisée n’importe où dans une disposition d’application et est plus personnalisable qu’une barre d’action.

![Exemple de barre d’outils](images/toolbar.png)

## <a name="viewpager"></a>[ViewPager](~/android/user-interface/controls/view-pager/index.md) 

Le `ViewPager` est un gestionnaire de disposition qui permet à l’utilisateur de retourner des pages de données à gauche et à droite.

![Exemple de ViewPager](images/viewpager.png)

## <a name="webview"></a>[Affichage Web](~/android/user-interface/controls/web-view.md)

`WebView` est un élément d’interface utilisateur qui vous permet de créer votre propre fenêtre pour afficher des pages Web (ou même développer un navigateur complet).

![Exemple d’affichage Web](images/web-view.png)
