---
title: Résumé du chapitre 6. Clics de bouton
description: 'Création d’applications mobiles avec Xamarin.Forms: Résumé du chapitre 6. Clics de bouton'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
ms.openlocfilehash: 12c8cdc19f9e6765ca25ade97bcfdbffb7b60381
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "61334717"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Résumé du chapitre 6. Clics de bouton

[![Télécharger](~/media/shared/download.png) l’échantillon Télécharger l’échantillon](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

La [`Button`](xref:Xamarin.Forms.Button) vue est celle qui permet à l’utilisateur d’initier une commande. A `Button` est identifié par texte (et optionnellement une image comme démontré dans [le chapitre 13, Bitmaps](chapter13.md)). Par conséquent, `Button` définit bon nombre `Label`des mêmes propriétés que :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button`définit également trois propriétés qui régissent l’apparence de sa frontière, mais le soutien de ces propriétés et leur indépendance mutuelle est spécifique à la plate-forme :

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor)de type`Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth)de type`Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius)de type`Double`

`Button`hérite également de `VisualElement` toutes `View`les `BackgroundColor` `HorizontalOptions`propriétés `VerticalOptions`de et , y compris , et .

## <a name="processing-the-click"></a>Traitement du clic

La `Button` classe définit [`Clicked`](xref:Xamarin.Forms.Button.Clicked) un événement qui est tiré `Button`lorsque l’utilisateur tape sur le . Le `Click` gestionnaire est `EventHandler`de type . Le premier argument `Button` est l’objet générant l’événement; le deuxième argument `EventArgs` est un objet qui ne fournit aucune information supplémentaire.

L’échantillon [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) `Clicked` démontre une manipulation simple.

## <a name="sharing-button-clicks"></a>Partage des clics de bouton

Plusieurs `Button` vues peuvent `Clicked` partager le même gestionnaire, mais `Button` le gestionnaire doit généralement déterminer qui est responsable d’un événement particulier. Une approche consiste à `Button` stocker les différents objets comme champs et vérifier lequel tire l’événement dans le gestionnaire.

[**L’échantillon TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) démontre cette technique. Le programme montre également comment [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) définir `Button` la `false` propriété `Button` d’un à lorsque le fait de pression sur le n’est plus valide. Un `Button` handicapé ne `Clicked` génère pas d’événement.

## <a name="anonymous-event-handlers"></a>Gestionnaires d’événements anonymes

Il est possible `Clicked` de définir les gestionnaires comme des fonctions lambda anonymes, comme le montre l’échantillon [**ButtonLambdas.**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) Cependant, les gestionnaires anonymes ne peuvent pas être partagés sans un code de réflexion désordonné.

## <a name="distinguishing-views-with-ids"></a>Distinguer les points de vue avec les DIU

Plusieurs `Button` objets peuvent également être [`StyleId`](xref:Xamarin.Forms.Element.StyleId) distingués en fixant la propriété ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) la propriété à un `string`. Cette propriété est `Element` définie par mais elle n’est pas utilisée dans Xamarin.Forms. Il est destiné à être utilisé uniquement par les programmes d’application.

[**L’échantillon SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) utilise le même gestionnaire d’événements pour les 10 clés `StyleId` de numéro sur un clavier numérique et les distingue avec la propriété :

[![Triple capture d’écran du clavier le plus simple](images/ch06fg04-small.png "Calculatrice")](images/ch06fg04-large.png#lightbox "Calculatrice")

## <a name="saving-transient-data"></a>Enregistrement de données transitoires

De nombreuses applications doivent enregistrer des données lorsqu’un programme est terminé et recharger ces données lorsque le programme reprend. La [`Application`](xref:Xamarin.Forms.Application) classe définit plusieurs membres qui aident votre programme à enregistrer et à restaurer des données transitoires :

- La [`Properties`](xref:Xamarin.Forms.Application.Properties) propriété est `string` un `object` dictionnaire avec des clés et des articles. Le contenu du dictionnaire est automatiquement enregistré dans le stockage local d’application avant la résiliation du programme, et rechargé lorsque le programme démarre.
- La `Application` classe définit trois méthodes virtuelles protégées que la [`OnStart`](xref:Xamarin.Forms.Application.OnStart) [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep)classe [`OnResume`](xref:Xamarin.Forms.Application.OnResume)standard `App` du programme l’emporte : , , et . Il s’agit d’événements *de cycle de vie de l’application.*
- La [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode enregistre le contenu du dictionnaire.

Il n’est `SavePropertiesAsync`pas nécessaire d’appeler . Le contenu du dictionnaire est automatiquement enregistré avant la fin du programme et récupéré avant le démarrage du programme. Il est utile pendant les tests de programme pour enregistrer des données si le programme se bloque.

Également utile est:

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), une propriété statique `Application` qui renvoie l’objet `Properties` actuel que vous pouvez ensuite utiliser pour obtenir le dictionnaire.

La première étape consiste à identifier toutes les variables de la page que vous souhaitez persister lorsque le programme prendra fin. Si vous connaissez tous les endroits où ces variables `Properties` changent, vous pouvez simplement les ajouter au dictionnaire à ce moment-là. Dans le constructeur de la page, vous `Properties` pouvez définir les variables à partir du dictionnaire si la clé existe.

Un programme plus vaste devra probablement faire face aux événements du cycle de vie des applications. Le plus important `OnSleep` est la méthode. Un appel à cette méthode indique que le programme a quitté le premier plan. Peut-être que l’utilisateur a appuyé sur le bouton **Accueil** sur l’appareil, ou affiché toutes les applications, ou est l’arrêt du téléphone. Un appel `OnSleep` est la seule notification qu’un programme reçoit avant sa fin. Le programme devrait profiter de `Properties` cette occasion pour s’assurer que le dictionnaire est à jour.

Un appel `OnResume` pour indique que le programme n’a pas pris fin après le dernier appel, `OnSleep` mais est maintenant en cours d’exécution au premier plan à nouveau. Le programme pourrait profiter de cette occasion pour actualiser les connexions Internet (par exemple).

Un appel `OnStart` à se produit pendant le démarrage du programme. Il n’est pas nécessaire d’attendre `Properties` que cette méthode appelle pour `App` accéder au dictionnaire parce que le contenu a déjà été restauré lorsque le constructeur est appelé.

[**L’échantillon PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) est très similaire à **SimplestKeypad,** sauf que le programme utilise le `OnSleep` remplacement pour enregistrer l’entrée actuelle du clavier, et le constructeur de pages pour restaurer ces données.

> [!NOTE]
> Une autre approche de l’enregistrement des paramètres du programme est fournie par la classe Xamarin.Essentials [Preferences.](~/essentials/preferences.md)

## <a name="related-links"></a>Liens connexes

- [Chapitre 6 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Échantillons du chapitre 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Échantillons du chapitre 6 F](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Bouton Xamarin.Forms](~/xamarin-forms/user-interface/button.md)
