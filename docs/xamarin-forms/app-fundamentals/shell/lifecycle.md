---
title : " Xamarin.Forms Description de l’interpréteur de commandes" : "les applications Shell respectent le Xamarin.Forms cycle de vie et un événement apparaissant est déclenché quand une page est sur le point d’apparaître sur l’écran, et un événement de disparition est déclenché quand une page est sur le point de disparaître de l’écran."
ms. Prod : xamarin ms. AssetID : 4E4EE50E-3BB4-441D-8355-CD9CD26ED1D0 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 07/25/2019 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-shell-lifecycle"></a>Xamarin.FormsCycle de vie du shell

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)

Les applications de Shell respectent le Xamarin.Forms cycle de vie et un `Appearing` événement est déclenché lorsqu’une page est sur le point d’apparaître sur l’écran, et un `Disappearing` événement est déclenché lorsqu’une page est sur le point de disparaître de l’écran. Ces événements sont propagés aux pages et peuvent être gérés en remplaçant les [`OnAppearing`](xref:Xamarin.Forms.Page.OnAppearing) [`OnDisappearing`](xref:Xamarin.Forms.Page.OnDisappearing) méthodes ou sur la page.

> [!NOTE]
> Dans une application Shell, les événements `Appearing` et `Disappearing` sont déclenchés à partir du code multiplateforme, avant que le code de plateforme rende une page visible ou supprime une page de l’écran.

Pour plus d’informations sur le Xamarin.Forms cycle de vie des applications, consultez [ Xamarin.Forms cycle de vie des applications](~/xamarin-forms/app-fundamentals/app-lifecycle.md).

## <a name="hierarchical-navigation"></a>Navigation hiérarchique

Dans une application Shell, l’envoi (push) d’une page sur la pile de navigation entraîne que l’objet `ShellContent` actuellement visible, et le contenu de sa page, déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page depuis la pile de navigation entraîne que l’objet `ShellContent` nouvellement visible, et le contenu de sa page, déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation hiérarchique, consultez [ Xamarin.Forms navigation hiérarchique](~/xamarin-forms/app-fundamentals/navigation/hierarchical.md).

## <a name="modal-navigation"></a>Navigation modale

Dans une application Shell, l’envoi d’une page modale sur la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Disappearing`. De même, l’affichage de la dernière page modale depuis la pile de navigation modale entraîne que tous les objets Shell visibles déclenchent l’événement `Appearing`.

Pour plus d’informations sur la navigation modale, consultez [ Xamarin.Forms pages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md).

## <a name="related-links"></a>Liens connexes

- [Xaminals (exemple)](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/userinterface-xaminals/)
- [Xamarin.FormsCycle de vie des applications](~/xamarin-forms/app-fundamentals/app-lifecycle.md)
- [Xamarin.FormsPages modales](~/xamarin-forms/app-fundamentals/navigation/modal.md)
