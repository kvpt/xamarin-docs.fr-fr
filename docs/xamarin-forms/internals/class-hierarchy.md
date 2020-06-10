---
title : " Xamarin.Forms Controls Hierarchy Class" Description : "les développeurs doivent être familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une Xamarin.Forms application."
ms. Prod : xamarin ms. AssetID : C89E6B98-464D-4BBE-BF11-13A5FCBBF420 ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 01/07/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="xamarinforms-controls-class-hierarchy"></a>Xamarin.FormsHiérarchie des classes de contrôles

Xamarin.Formsest constitué de centaines de types, sur plusieurs espaces de noms. Les développeurs doivent être plus familiarisés avec la hiérarchie de types utilisée pour créer l’interface utilisateur d’une Xamarin.Forms application, qui réside dans l' `Xamarin.Forms` espace de noms.

Ces types peuvent être divisés en pages, dispositions, vues et cellules. Une Xamarin.Forms page occupe généralement la totalité de l’écran, et tous les types de pages dérivent de la [`Page`](xref:Xamarin.Forms.Page) classe. Les pages contiennent généralement une disposition, et tous les types de disposition dérivent de la [`Layout`](xref:Xamarin.Forms.Layout) classe. Une disposition contient généralement des vues et éventuellement d’autres dispositions, et tous les types d’affichages dérivent finalement de la [`View`](xref:Xamarin.Forms.View) classe. Enfin, les cellules sont des contrôles spécialisés qui sont utilisés dans les données d’affichage des [`TableView`](xref:Xamarin.Forms.TableView) [`ListView`](xref:Xamarin.Forms.ListView) contrôles et. Les pages, les dispositions, les vues et les cellules sont finalement dérivés de la [`Element`](xref:Xamarin.Forms.Element) classe.

Le diagramme de classes suivant illustre la hiérarchie des types généralement utilisés pour créer une interface utilisateur dans Xamarin.Forms :

[![Xamarin.FormsDiagramme de classes de contrôles](class-hierarchy-images/class-diagram.png "[! Opérationnel. NO-LOC (Xamarin. Forms)] diagrammes de classes de contrôles")](class-hierarchy-images/class-diagram-large.png#lightbox "[! Opérationnel. NO-LOC (Xamarin. Forms)] diagrammes de classes de contrôles")

> [!NOTE]
> Une version haute résolution du diagramme de classes peut être téléchargée à partir d' [ici](class-hierarchy-images/class-diagram-high-resolution.png). Toutefois, Notez que le diagramme n’affiche qu’un seul type d’interpréteur de commandes.

## <a name="related-links"></a>Liens connexes

- [Xamarin.FormsRéférence des contrôles](~/xamarin-forms/user-interface/controls/index.md)
