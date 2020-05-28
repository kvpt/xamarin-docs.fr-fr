---
titre : Description : « cet article explique la prise en charge de la plate-forme Windows qui est disponible dans Xamarin.Forms . »
ms. Prod : ms. AssetID : ms. Technology : Auteur : ms. Author : ms. Date : No-Loc :
- 'Xamarin.Forms'
- 'Xamarin.Essentials'

---

# <a name="windows-platform-features"></a>Fonctionnalités de la plateforme Windows

Le développement Xamarin.Forms d’applications pour les plateformes Windows nécessite Visual Studio. La [page plateformes prises en charge](~/get-started/supported-platforms.md) contient des informations supplémentaires sur les conditions préalables.

![](images/allhanselman.png "Xamarin.Forms Applications Running on Windows")

## <a name="platform-specifics"></a>Spécificités des plateformes

Les spécificités des plateformes vous permettent d’utiliser des fonctionnalités uniquement disponibles sur une plateforme spécifique, sans implémenter de convertisseurs ou d’effets personnalisés.

Les fonctionnalités suivantes propres à la plateforme sont fournies pour Xamarin.Forms les affichages, les pages et les mises en page de la plateforme Windows universelle (UWP) :

- Définition d’une clé d’accès pour un [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [clés d’accès VisualElement sur Windows](visualelement-access-keys.md).
- Désactivation du mode de couleurs hérité sur un pris en charge [`VisualElement`](xref:Xamarin.Forms.VisualElement) . Pour plus d’informations, consultez [VisualElement Legacy Color mode on Windows](legacy-color-mode.md).

Les fonctionnalités spécifiques à la plateforme suivantes sont fournies pour les Xamarin.Forms affichages sur UWP :

- Détection de l’ordre de lecture à partir du contenu de texte dans [`Entry`](xref:Xamarin.Forms.Entry) [`Editor`](xref:Xamarin.Forms.Editor) les instances, et [`Label`](xref:Xamarin.Forms.Label) . Pour plus d’informations, consultez [ordre de lecture de InputView sur Windows](inputview-reading-order.md).
- Activation de la prise en charge des gestes TAP dans un [`ListView`](xref:Xamarin.Forms.ListView) . Pour plus d’informations, consultez [ListView SelectionMode sur Windows](listview-selectionmode.md).
- Activation de la direction de l’extraction d’un `RefreshView` à modifier. Pour plus d’informations, consultez [direction RefreshView pull sur Windows](refreshview-pulldirection.md).
- Activation [`SearchBar`](xref:Xamarin.Forms.SearchBar) d’un pour interagir avec le moteur de vérification orthographique. Pour plus d’informations, consultez [SearchBar vérification orthographique sur Windows](searchbar-spell-check.md).
- Activation [`WebView`](xref:Xamarin.Forms.WebView) d’un pour afficher des alertes JavaScript dans une boîte de dialogue de message UWP. Pour plus d’informations, consultez [afficher des alertes JavaScript sur Windows](webview-javascript-alert.md).

Les fonctionnalités suivantes propres à la plateforme sont fournies pour les Xamarin.Forms pages sur UWP :

- Réduction de la [`MasterDetailPage`](xref:Xamarin.Forms.MasterDetailPage) barre de navigation. Pour plus d’informations, consultez [barre de navigation MasterDetailPage sur Windows](masterdetailpage-navigation-bar.md).
- Définition des options de positionnement de la barre d’outils. Pour plus d’informations, consultez [positionnement de la barre d’outils de la page sur Windows](page-toolbar-placement.md).
- Activation des icônes de page à afficher dans une [`TabbedPage`](xref:Xamarin.Forms.TabbedPage) barre d’outils. Pour plus d’informations, consultez [TabbedPage icons on Windows](tabbedpage-icons.md).

La fonctionnalité spécifique à la plateforme suivante est fournie pour la Xamarin.Forms [`Application`](xref:Xamarin.Forms.Application) classe sur UWP :

- Spécifie le répertoire du projet à partir duquel les ressources d’images seront chargées. Pour plus d’informations, consultez [répertoire d’images par défaut sur Windows](default-image-directory.md).

## <a name="platform-support"></a>Plateforme prise en charge

Les Xamarin.Forms modèles disponibles dans Visual Studio contiennent un projet plateforme Windows universelle (UWP).

> [!NOTE]
> Xamarin.Forms1. x et 2. x prennent en charge _Windows Phone 8 Silverlight_, _Windows Phone 8,1_et le développement d’applications _Windows 8.1_ . Toutefois, ces types de projets ont été dépréciés.

## <a name="getting-started"></a>Prise en main

Accédez à **fichier > nouveau projet >** dans Visual Studio et choisissez l’un des modèles **multiplateforme > application vide ( Xamarin.Forms )** pour commencer.

Xamarin.FormsLes anciennes solutions, ou celles créées sur MacOS, n’ont pas tous les projets Windows listés ci-dessus (mais elles doivent être ajoutées manuellement). Si la plateforme Windows que vous souhaitez cibler ne figure pas dans votre solution, consultez les [instructions d’installation](installation/index.md) pour ajouter le ou les types de projet Windows souhaités.

## <a name="samples"></a>Exemples

[Tous les exemples de la](https://github.com/xamarin/xamarin-forms-book-preview-2) documentation de Charles Petzold qui [*crée Xamarin.Forms des Mobile Apps avec*](~/xamarin-forms/creating-mobile-apps-xamarin-forms/index.md) incluent des projets plateforme Windows universelle (pour Windows 10).

L' [application de démonstration « Scott Hanselman »](https://github.com/jamesmontemagno/Hanselman.Forms) est disponible séparément et comprend également les projets Apple Watch et Android usure (à l’aide de Xamarin. iOS et Xamarin. Android, Xamarin.Forms ne s’exécutent pas sur ces plateformes).

## <a name="related-links"></a>Liens connexes

- [Configurer des projets Windows](~/xamarin-forms/platform/windows/installation/index.md)
