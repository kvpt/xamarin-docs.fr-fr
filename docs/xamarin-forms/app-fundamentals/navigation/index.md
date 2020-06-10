---
titre : « Xamarin.Forms navigation » Description : «ce guide explique comment effectuer la navigation dans les Xamarin.Forms applications. Xamarin.Formsfournit un certain nombre d’expériences de navigation entre les pages différentes, en fonction du type de page utilisé.»
ms. Prod : xamarin ms. AssetID : BC5D0C6C-D5A9-4B12-A492-ED1F570CEC87 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 12/01/2017 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-navigation"></a>Xamarin.FormsDéplacement

_Xamarin. Forms fournit un certain nombre d’expériences de navigation entre les pages différentes, en fonction du type de page utilisé._

![](images/page-types.png "Xamarin.Forms Page Types")

Les Xamarin.Forms applications Shell utilisent également une expérience de navigation basée sur un URI qui n’applique pas de hiérarchie de navigation définie. Pour plus d’informations, consultez navigation dans le [ Xamarin.Forms Shell](~/xamarin-forms/app-fundamentals/shell/navigation.md).

## <a name="hierarchical-navigation"></a>[Navigation hiérarchique](hierarchical.md)

La [`NavigationPage`](xref:Xamarin.Forms.NavigationPage) classe fournit une expérience de navigation hiérarchique dans laquelle l’utilisateur peut naviguer dans les pages, vers l’avant et vers l’arrière, selon les besoins. La classe implémente la navigation sous la forme d’une pile d’objets LIFO (dernier entré, premier sorti) [`Page`](xref:Xamarin.Forms.Page) .

## <a name="tabbedpage"></a>[TabbedPage](tabbed-page.md)

Le Xamarin.Forms [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) se compose d’une liste d’onglets et d’une zone de détails plus grande, chaque onglet chargeant du contenu dans la zone de détails.

## <a name="carouselpage"></a>[CarouselPage](carousel-page.md)

Xamarin.Forms [`CarouselPage`](xref:Xamarin.Forms.CarouselPage) Est une page que les utilisateurs peuvent balayer de côté à côté pour naviguer dans les pages de contenu, comme une galerie.

## <a name="masterdetailpage"></a>[MasterDetailPage](master-detail-page.md)

Xamarin.Forms [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) Est une page qui gère deux pages d’informations associées : une page maître qui présente des éléments et une page de détails qui présente des détails sur les éléments de la page maître.

## <a name="modal-pages"></a>[Pages modales](modal.md)

Xamarin.Formsprend également en charge les pages modales. Une page modale encourage les utilisateurs à effectuer une tâche autonome et ne peut pas être quittée tant que la tâche n’est pas terminée ou annulée.
