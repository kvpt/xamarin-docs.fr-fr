---
title: Résumé du chapitre 6. Clics de bouton
description: 'Création d’Mobile Apps avec Xamarin.Forms : Résumé du chapitre 6. Clics de bouton'
ms.prod: xamarin
ms.technology: xamarin-forms
ms.assetid: D4F9C429-A6CF-40FA-AC68-3F149307A5F9
author: davidbritch
ms.author: dabritch
ms.date: 07/18/2018
no-loc:
- Xamarin.Forms
- Xamarin.Essentials
ms.openlocfilehash: 8a95f4a88ea0adff78475dab8699308fbe49aa46
ms.sourcegitcommit: ebdc016b3ec0b06915170d0cbbd9e0e2469763b9
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93370206"
---
# <a name="summary-of-chapter-6-button-clicks"></a>Résumé du chapitre 6. Clics de bouton

[![Télécharger l’exemple](~/media/shared/download.png) Télécharger l’exemple](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)

> [!NOTE]
> Ce livre a été publié à la printemps de 2016 et n’a pas été mis à jour depuis. Le livre reste très utile, mais certains d’entre eux sont obsolètes et certaines rubriques ne sont plus entièrement correctes ou complètes.

[`Button`](xref:Xamarin.Forms.Button)Est la vue qui permet à l’utilisateur de lancer une commande. Un `Button` est identifié par du texte (et éventuellement une image, comme illustré dans le [chapitre 13, bitmaps](chapter13.md)). Par conséquent, `Button` définit un grand nombre des mêmes propriétés que `Label` :

- [`Text`](xref:Xamarin.Forms.Button.Text)
- [`FontFamily`](xref:Xamarin.Forms.Button.FontFamily)
- [`FontSize`](xref:Xamarin.Forms.Button.FontSize)
- [`FontAttributes`](xref:Xamarin.Forms.Button.FontAttributes)
- [`TextColor`](xref:Xamarin.Forms.Button.TextColor)

`Button` définit également trois propriétés qui régissent l’apparence de sa bordure, mais la prise en charge de ces propriétés et leur indépendance mutuelle sont spécifiques à la plateforme :

- [`BorderColor`](xref:Xamarin.Forms.Button.BorderColor) de type `Color`
- [`BorderWidth`](xref:Xamarin.Forms.Button.BorderWidth) de type `Double`
- [`BorderRadius`](xref:Xamarin.Forms.Button.BorderRadius) de type `Double`

`Button` hérite également de toutes les propriétés de `VisualElement` et `View` , y compris `BackgroundColor` , `HorizontalOptions` et `VerticalOptions` .

## <a name="processing-the-click"></a>Traitement du clic

La `Button` classe définit un [`Clicked`](xref:Xamarin.Forms.Button.Clicked) événement qui est déclenché quand l’utilisateur appuie sur `Button` . Le `Click` gestionnaire est de type `EventHandler` . Le premier argument est l' `Button` objet qui génère l’événement ; le deuxième argument est un `EventArgs` objet qui ne fournit aucune information supplémentaire.

L’exemple [**ButtonLogger**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLogger) illustre la `Clicked` gestion simple.

## <a name="sharing-button-clicks"></a>Partage des clics de bouton

Plusieurs `Button` vues peuvent partager le même `Clicked` Gestionnaire, mais le gestionnaire doit généralement déterminer celui qui `Button` est responsable d’un événement particulier. Une approche consiste à stocker les différents `Button` objets en tant que champs et à vérifier celui qui déclenche l’événement dans le gestionnaire.

L’exemple [**TwoButtons**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/TwoButtons) illustre cette technique. Le programme montre également comment affecter à la [`IsEnabled`](xref:Xamarin.Forms.VisualElement.IsEnabled) propriété d’un la valeur `Button` `false` lorsque l’appui sur `Button` n’est plus valide. Un désactivé `Button` ne génère pas d' `Clicked` événement.

## <a name="anonymous-event-handlers"></a>Gestionnaires d’événements anonymes

Il est possible de définir des `Clicked` gestionnaires comme fonctions lambda anonymes, comme le montre l’exemple [**ButtonLambdas**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/ButtonLambdas) . Toutefois, les gestionnaires anonymes ne peuvent pas être partagés sans code de réflexion en désordre.

## <a name="distinguishing-views-with-ids"></a>Distinction entre les vues et les ID

Il est `Button` également possible de distinguer plusieurs objets en affectant [`StyleId`](xref:Xamarin.Forms.Element.StyleId) à la propriété ou [`AutomationId`](xref:Xamarin.Forms.Element.AutomationId) à la propriété `string` . Cette propriété est définie par, `Element` mais elle n’est pas utilisée dans Xamarin.Forms . Il est destiné à être utilisé uniquement par les programmes d’application.

L’exemple [**SimplestKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/SimplestKeypad) utilise le même gestionnaire d’événements pour toutes les 10 touches numériques sur un pavé numérique et le distingue de la `StyleId` propriété :

[![Capture d’écran triple du pavé numérique le plus simple](images/ch06fg04-small.png "Calculatrice")](images/ch06fg04-large.png#lightbox "Calculatrice")

## <a name="saving-transient-data"></a>Enregistrement de données temporaires

De nombreuses applications doivent enregistrer des données lorsqu’un programme est terminé et recharger ces données au redémarrage du programme. La [`Application`](xref:Xamarin.Forms.Application) classe définit plusieurs membres qui aident votre programme à enregistrer et à restaurer des données temporaires :

- La [`Properties`](xref:Xamarin.Forms.Application.Properties) propriété est un dictionnaire avec des `string` clés et des `object` éléments. Le contenu du dictionnaire est automatiquement enregistré dans le stockage local de l’application avant l’arrêt du programme, puis rechargé au démarrage du programme.
- La `Application` classe définit trois méthodes virtuelles protégées que la classe standard du programme `App` substitue : [`OnStart`](xref:Xamarin.Forms.Application.OnStart) , [`OnSleep`](xref:Xamarin.Forms.Application.OnSleep) et [`OnResume`](xref:Xamarin.Forms.Application.OnResume) . Ils font référence aux événements *du cycle de vie* de l’application.
- La [`SavePropertiesAsync`](xref:Xamarin.Forms.Application.SavePropertiesAsync) méthode enregistre le contenu du dictionnaire.

Il n’est pas nécessaire d’appeler `SavePropertiesAsync` . Le contenu du dictionnaire est automatiquement enregistré avant la fin du programme et récupéré avant le démarrage du programme. Elle est utile lors du test du programme pour enregistrer des données en cas de blocage du programme.

Il est également utile :

- [`Application.Current`](xref:Xamarin.Forms.Application.Current), propriété statique qui retourne l’objet actuel `Application` que vous pouvez ensuite utiliser pour obtenir le `Properties` dictionnaire.

La première étape consiste à identifier toutes les variables de la page que vous souhaitez conserver quand le programme se termine. Si vous connaissez tous les endroits où ces variables changent, vous pouvez simplement les ajouter au `Properties` dictionnaire à ce stade. Dans le constructeur de la page, vous pouvez définir les variables à partir du `Properties` dictionnaire si la clé existe.

Un programme plus volumineux aura probablement besoin de gérer les événements de cycle de vie des applications. La plus importante est la `OnSleep` méthode. Un appel à cette méthode indique que le programme a quitté le premier plan. Peut-être que l’utilisateur a appuyé sur le bouton de **démarrage** de l’appareil ou affiché toutes les applications, ou qu’il arrête le téléphone. Un appel à `OnSleep` est la seule notification qu’un programme reçoit avant qu’il ne soit arrêté. Le programme doit prendre cette occasion pour s’assurer que le `Properties` dictionnaire est à jour.

Un appel à `OnResume` indique que le programme ne s’est pas arrêté après le dernier appel à `OnSleep` mais s’exécute à nouveau au premier plan. Le programme peut utiliser cette opportunité pour actualiser les connexions Internet (par exemple,).

Un appel à `OnStart` se produit pendant le démarrage du programme. Il n’est pas nécessaire d’attendre que cet appel de méthode accède au `Properties` dictionnaire, car le contenu a déjà été restauré lorsque le `App` constructeur est appelé.

L’exemple [**PersistentKeypad**](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/PersistentKeypad) est très similaire à **SimplestKeypad** , à ceci près que le programme utilise le `OnSleep` remplacement pour enregistrer l’entrée de pavé numérique active et le constructeur de page pour restaurer ces données.

> [!NOTE]
> Une autre approche de l’enregistrement des paramètres de programme est fournie par la Xamarin.Essentials classe [Preferences](~/essentials/preferences.md) .

## <a name="related-links"></a>Liens connexes

- [Chapitre 6 texte intégral (PDF)](https://download.xamarin.com/developer/xamarin-forms-book/XamarinFormsBook-Ch06-Apr2016.pdf)
- [Exemples du chapitre 6](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06)
- [Exemples de chapitre 6 F #](https://github.com/xamarin/xamarin-forms-book-samples/tree/master/Chapter06/FS)
- [Xamarin.Forms Bouton](~/xamarin-forms/user-interface/button.md)
