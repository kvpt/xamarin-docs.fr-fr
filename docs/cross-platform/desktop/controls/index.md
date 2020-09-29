---
ms.assetid: 4D47185C-8998-4903-AE64-7E2A67F9DF7A
title: Comparaison des contrôles d’interface utilisateur
description: Ce document fournit une comparaison entre les contrôles d’interface utilisateur entre Xamarin. Forms, Windows Forms et WPF. Il contient également des liens vers d’autres documents qui comparent WPF à Xamarin. Forms.
author: davidortinau
ms.author: daortin
ms.date: 04/26/2017
ms.openlocfilehash: b4cffd9e95f24dea9fc5fed5a6badeec624a4e25
ms.sourcegitcommit: 4e399f6fa72993b9580d41b93050be935544ffaa
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/29/2020
ms.locfileid: "91453088"
---
# <a name="ui-controls-comparison"></a>Comparaison des contrôles d’interface utilisateur

Vous trouverez ci-dessous une comparaison des contrôles Xamarin. Forms avec Windows Forms et WPF, basés sur [cette table](/dotnet/framework/wpf/advanced/windows-forms-controls-and-equivalent-wpf-controls).

En savoir plus sur les [similitudes et les différences entre WPF et Xamarin. Forms](wpf.md) pour vous aider à mettre à jour vos connaissances de bureau pour le développement d’applications mobiles.

|Windows Forms|WPF|Xamarin.Forms|
|--- |--- |--- |
|[BindingNavigator](/dotnet/api/system.windows.forms.bindingnavigator)|-|-|
|[BindingSource](/dotnet/api/system.windows.forms.bindingsource)|[CollectionViewSource](/dotnet/api/system.windows.data.collectionviewsource)|Propriété de liaison, par exemple. BindingContext|
|[Button](/dotnet/api/system.windows.forms.button)|[Button](/dotnet/api/system.windows.controls.button)|Bouton|
|[CheckBox](/dotnet/api/system.windows.forms.checkbox)|[CheckBox](/dotnet/api/system.windows.controls.checkbox)|Basculer|
|[CheckedListBox](/dotnet/api/system.windows.forms.checkedlistbox)|[ListBox](/dotnet/api/system.windows.controls.listbox) avec composition.|ListView avec composition.|
|[ColorDialog](/dotnet/api/system.windows.forms.colordialog)|-|-|
|[ComboBox](/dotnet/api/system.windows.forms.combobox)|[ComboBox](/dotnet/api/system.windows.controls.combobox) (ne prend pas en charge la saisie semi-automatique)|Sélecteur|
|[ContextMenuStrip](/dotnet/api/system.windows.forms.contextmenustrip)|[ContextMenu](/dotnet/api/system.windows.controls.contextmenu)|-|
|[DataGridView](/dotnet/api/system.windows.forms.datagridview)|[DataGrid](/dotnet/api/system.windows.controls.datagrid)|-|
|[DateTimePicker](/dotnet/api/system.windows.forms.datetimepicker)|[DatePicker](/dotnet/api/system.windows.controls.datepicker)|DatePicker & TimePicker|
|[DomainUpDown](/dotnet/api/system.windows.forms.domainupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) et deux contrôles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Exécution pas à pas|
|[ErrorProvider](/dotnet/api/system.windows.forms.errorprovider)|-|-|
|[FlowLayoutPanel](/dotnet/api/system.windows.forms.flowlayoutpanel)|[WrapPanel](/dotnet/api/system.windows.controls.wrappanel) ou [StackPanel](/dotnet/api/system.windows.controls.stackpanel)|StackLayout ou FlexLayout|
|[FolderBrowserDialog](/dotnet/api/system.windows.forms.folderbrowserdialog)|-|-|
|[FontDialog](/dotnet/api/system.windows.forms.fontdialog)|-|-|
|[Forme](/dotnet/api/system.windows.forms.form)|[Window](/dotnet/api/system.windows.window)|Page|
|[GroupBox](/dotnet/api/system.windows.forms.groupbox)|[GroupBox](/dotnet/api/system.windows.controls.groupbox)|-|
|[HelpProvider](/dotnet/api/system.windows.forms.helpprovider)|Aucun contrôle équivalent (utilisez des info-bulles).|-|
|[HScrollBar](/dotnet/api/system.windows.forms.hscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar) (le défilement est intégré aux contrôles conteneurs)|utiliser ScrollView|
|[ImageList](/dotnet/api/system.windows.forms.imagelist)|-|-|
|[Étiquette](/dotnet/api/system.windows.forms.label)|[Étiquette](/dotnet/api/system.windows.controls.label)|Etiquette|
|[LinkLabel](/dotnet/api/system.windows.forms.linklabel)|Aucun contrôle équivalent (vous pouvez utiliser la classe [LIEN_HYPERTEXTE](/dotnet/api/system.windows.documents.hyperlink) pour héberger des liens hypertexte dans le contenu dynamique).|-|
|[ListBox](/dotnet/api/system.windows.forms.listbox)|[ListBox](/dotnet/api/system.windows.controls.listbox)|Utiliser ListView|
|[ListView](/dotnet/api/system.windows.forms.listview)|[ListView](/dotnet/api/system.windows.controls.listview)|Affichage de liste|
|[MaskedTextBox](/dotnet/api/system.windows.forms.maskedtextbox)|-|-|
|[MenuStrip](/dotnet/api/system.windows.forms.menustrip)|[Menu](/dotnet/api/system.windows.controls.menu)|Utilisez MasterDetailPage ou TabbedPage|
|[MonthCalendar](/dotnet/api/system.windows.forms.monthcalendar)|[Calendrier](/dotnet/api/system.windows.controls.calendar)|-|
|[NotifyIcon](/dotnet/api/system.windows.forms.notifyicon)|-|-|
|[NumericUpDown](/dotnet/api/system.windows.forms.numericupdown)|[TextBox](/dotnet/api/system.windows.controls.textbox) et deux contrôles [RepeatButton](/dotnet/api/system.windows.controls.primitives.repeatbutton) .|Exécution pas à pas|
|[OpenFileDialog](/dotnet/api/system.windows.forms.openfiledialog)|[OpenFileDialog](/dotnet/api/microsoft.win32.openfiledialog)|-|
|[PageSetupDialog](/dotnet/api/system.windows.forms.pagesetupdialog)|-|-|
|[Panel](/dotnet/api/system.windows.forms.panel)|[Canevas](/dotnet/api/system.windows.controls.canvas)|Afficher ou AbsoluteLayout|
|[PictureBox](/dotnet/api/system.windows.forms.picturebox)|[Image](/dotnet/api/system.windows.controls.image)|Image|
|[PrintDialog](/dotnet/api/system.windows.forms.printdialog)|[PrintDialog](/dotnet/api/system.windows.controls.printdialog)|-|
|[PrintDocument](/dotnet/api/system.drawing.printing.printdocument)|-|-|
|[PrintPreviewControl](/dotnet/api/system.windows.forms.printpreviewcontrol)|[DocumentViewer](/dotnet/api/system.windows.controls.documentviewer)|-|
|[PrintPreviewDialog](/dotnet/api/system.windows.forms.printpreviewdialog)|-|-|
|[Barre de progression](/dotnet/api/system.windows.forms.progressbar)|[ProgressBar](/dotnet/api/system.windows.controls.progressbar)|Barre de progression|
|[PropertyGrid](/dotnet/api/system.windows.forms.propertygrid)|-|-|
|[RadioButton](/dotnet/api/system.windows.forms.radiobutton)|[RadioButton](/dotnet/api/system.windows.controls.radiobutton)|-|
|[RichTextBox](/dotnet/api/system.windows.forms.richtextbox)|[RichTextBox](/dotnet/api/system.windows.controls.richtextbox)|L’éditeur ne prend pas en charge le texte enrichi (mis en forme), l’entrée pour le texte d’une seule ligne|
|[SaveFileDialog](/dotnet/api/system.windows.forms.savefiledialog)|[SaveFileDialog](/dotnet/api/microsoft.win32.savefiledialog)|-|
|[ScrollableControl](/dotnet/api/system.windows.forms.scrollablecontrol)|[ScrollViewer](/dotnet/api/system.windows.controls.scrollviewer)|ScrollView|
|[SoundPlayer](/dotnet/api/system.media.soundplayer)|[MediaPlayer](/dotnet/api/system.windows.media.mediaplayer)|-|
|[SplitContainer](/dotnet/api/system.windows.forms.splitcontainer)|[GridSplitter](/dotnet/api/system.windows.controls.gridsplitter)|Envisagez MasterDetailPage|
|[StatusStrip](/dotnet/api/system.windows.forms.statusstrip)|[StatusBar](/dotnet/api/system.windows.controls.primitives.statusbar)|-|
|[TabControl](/dotnet/api/system.windows.forms.tabcontrol)|[TabControl](/dotnet/api/system.windows.controls.tabcontrol)|TabbedPage|
|[TableLayoutPanel](/dotnet/api/system.windows.forms.tablelayoutpanel)|[Grid](/dotnet/api/system.windows.controls.grid)|Grille|
|[TextBox](/dotnet/api/system.windows.forms.textbox)|[TextBox](/dotnet/api/system.windows.controls.textbox)|L’éditeur ne prend pas en charge le texte enrichi (mis en forme)|
|[Minuteur](/dotnet/api/system.windows.forms.timer)|[DispatcherTimer](/dotnet/api/system.windows.threading.dispatchertimer)|Device. StartTime ()|
|[ToolStrip](/dotnet/api/system.windows.forms.toolstrip)|[Barre](/dotnet/api/system.windows.controls.toolbar)|Page. ToolbarItems et ToolbarItem|
|[ToolStripContainer](/dotnet/api/system.windows.forms.toolstripcontainer), [ToolStripDropDown](/dotnet/api/system.windows.forms.toolstripdropdown), [ToolStripDropDownMenu](/dotnet/api/system.windows.forms.toolstripdropdownmenu), [ToolStripPanel](/dotnet/api/system.windows.forms.toolstrippanel)|[Barre d’outils](/dotnet/api/system.windows.controls.toolbar) avec composition.|Page. ToolbarItems et ToolbarItem avec composition|
|[ToolTip](/dotnet/api/system.windows.forms.tooltip)|[ToolTip](/dotnet/api/system.windows.controls.tooltip)|Utiliser les fonctionnalités d’accessibilité|
|[TrackBar](/dotnet/api/system.windows.forms.trackbar)|[Curseur](/dotnet/api/system.windows.controls.slider)|Curseur|
|[TreeView](/dotnet/api/system.windows.forms.treeview)|[TreeView](/dotnet/api/system.windows.controls.treeview)|Envisager le ListView hiérarchique dans un NavigationPage|
|[UserControl](/dotnet/api/system.windows.forms.usercontrol)|[UserControl](/dotnet/api/system.windows.controls.usercontrol)|Afficher et également les convertisseurs personnalisés|
|[VScrollBar](/dotnet/api/system.windows.forms.vscrollbar)|[ScrollBar](/dotnet/api/system.windows.controls.primitives.scrollbar)|utiliser ScrollView|
|[WebBrowser](/dotnet/api/system.windows.forms.webbrowser)|[WebBrowser](/dotnet/api/system.windows.controls.webbrowser)|WebView|