---
title: Utilisation de Xamarin. Android Designer
description: Cet article est une procédure pas à pas de Xamarin. Android Designer. Il montre comment créer une interface utilisateur pour une petite application d’Explorateur de couleurs ; Cette interface utilisateur est entièrement créée dans le concepteur.
ms.prod: xamarin
ms.assetid: 70FF2F9A-71BD-317E-C881-A44D82DF1BD8
ms.technology: xamarin-android
author: conceptdev
ms.author: crdun
ms.date: 07/25/2018
ms.openlocfilehash: a4867ef779e55d16ef49b2f8ad0dc5bd0ec7024f
ms.sourcegitcommit: 57f815bf0024b1afe9754c0e28054fc0a53ce302
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/06/2019
ms.locfileid: "70761451"
---
# <a name="using-the-xamarinandroid-designer"></a>Utilisation de Xamarin. Android Designer

_Cet article est une procédure pas à pas de Xamarin. Android Designer. Il montre comment créer une interface utilisateur pour une petite application d’Explorateur de couleurs ; Cette interface utilisateur est entièrement créée dans le concepteur._

## <a name="overview"></a>Présentation

Les interfaces utilisateur Android peuvent être créées de façon déclarative à l’aide de fichiers XML ou par programme en écrivant du code. Xamarin. Android Designer permet aux développeurs de créer et de modifier visuellement des dispositions déclaratives, sans devoir modifier manuellement les fichiers XML. Le concepteur fournit également des commentaires en temps réel qui permettent au développeur d’évaluer les modifications de l’interface utilisateur sans avoir à redéployer l’application sur un appareil ou un émulateur. Ces fonctionnalités de concepteur peuvent accélérer considérablement le développement de l’interface utilisateur Android.
Cet article explique comment utiliser Xamarin. Android Designer pour créer visuellement une interface utilisateur.

> [!TIP]
> Les nouvelles versions de Visual Studio prennent en charge l’ouverture de fichiers .xml dans Android Designer.
>
> Les fichiers .axml et .xml sont pris en charge dans Android Designer.

## <a name="walkthrough"></a>Procédure pas à pas

L’objectif de cette procédure pas à pas est d’utiliser la Android Designer pour créer une interface utilisateur pour un exemple d’application de navigateur couleur. L’application de l’Explorateur de couleurs présente une liste de couleurs, leurs noms et leurs valeurs RVB. Vous allez apprendre à ajouter des widgets au **aire de conception** , ainsi qu’à la disposition visuelle de ces widgets. Après cela, vous apprendrez à modifier les widgets de manière interactive sur la **aire de conception** ou à l’aide du volet **Propriétés** du concepteur. Enfin, vous verrez comment la conception se présente quand l’application s’exécute sur un appareil ou un émulateur.

# <a name="visual-studiotabwindows"></a>[Visual Studio](#tab/windows)

### <a name="creating-a-new-project"></a>Création d’un projet

La première étape consiste à créer un nouveau projet Xamarin. Android. Lancez Visual Studio, cliquez sur **nouveau projet...** , puis choisissez le modèle **\# Visual C > Android > Android App (Xamarin)** .
Nommez la nouvelle application **DesignerWalkthrough** , puis cliquez sur **OK**.

[![Application vide Android](designer-walkthrough-images/vs/01-android-app-w158-sml.png)](designer-walkthrough-images/vs/01-android-app-w158.png#lightbox)

Dans la boîte de dialogue **nouvelle application Android** , choisissez **application vide** , puis cliquez sur **OK**:

[![Sélection du modèle application Android vide](designer-walkthrough-images/vs/02-blank-app-w158-sml.png)](designer-walkthrough-images/vs/02-blank-app-w158.png#lightbox)

### <a name="adding-a-layout"></a>Ajout d’une disposition

L’étape suivante consiste à créer un **élément LinearLayout** qui contiendra les éléments de l’interface utilisateur. Cliquez avec le bouton droit sur **ressources/mise en page** dans le **Explorateur de solutions** puis sélectionnez **Ajouter > nouvel élément...** . Dans la boîte de dialogue **Ajouter un nouvel élément** , sélectionnez **mise en page Android**. Nommez le fichier **list_item** , puis cliquez sur **Ajouter**:

[![Nouvelle disposition](designer-walkthrough-images/vs/03-new-layout-w158-sml.png)](designer-walkthrough-images/vs/03-new-layout-w158.png#lightbox)

La nouvelle disposition **list_item** est affichée dans le concepteur. Notez que deux volets s’affichent &ndash; . le *aire de conception* pour le **list_item** est visible dans le volet gauche tandis que sa source XML est affichée dans le volet droit. Vous pouvez permuter les positions des volets **aire de conception** et **source** en cliquant sur l’icône d' **échange de volets** située entre les deux volets :

[![Mode concepteur](designer-walkthrough-images/vs/04-designer-view-w158-sml.png)](designer-walkthrough-images/vs/04-designer-view-w158.png#lightbox)

Dans le menu **affichage** , cliquez sur **autres fenêtres > structure du document** pour ouvrir la structure du **document**. La **structure du document** montre que la disposition contient actuellement un widget **élément LinearLayout** unique :

[![Structure du document](designer-walkthrough-images/vs/06-document-outline-w158-sml.png)](designer-walkthrough-images/vs/06-document-outline-w158.png#lightbox)

L’étape suivante consiste à créer l’interface utilisateur de l’application de navigateur couleur dans `LinearLayout`ce.

### <a name="creating-the-list-item-user-interface"></a>Création de l’interface utilisateur de l’élément de liste

Si le volet **boîte à outils** n’est pas affiché, cliquez sur l’onglet **boîte à outils** à gauche. Dans la **boîte à outils**, faites défiler jusqu’à la section **images & média** , puis faites `ImageView`défiler jusqu’à ce que vous trouviez :

[![Localiser ImageView](designer-walkthrough-images/vs/07-locate-imageview-w158-sml.png)](designer-walkthrough-images/vs/07-locate-imageview-w158.png#lightbox)

Vous pouvez également entrer *ImageView* dans la barre de recherche pour rechercher les `ImageView`éléments suivants :

[![Recherche ImageView](designer-walkthrough-images/vs/08-imageview-search-w158-sml.png)](designer-walkthrough-images/vs/08-imageview-search-w158.png#lightbox)

Faites-le glisser `ImageView` surleairedeconception(ilserautilisépourafficherunéchantillondecouleurdansl’applicationdel’Explorateurdecouleurs):`ImageView`

[![ImageView sur Canvas](designer-walkthrough-images/vs/09-imageview-on-canvas-w158-sml.png)](designer-walkthrough-images/vs/09-imageview-on-canvas-w158.png#lightbox)

Ensuite, faites glisser `LinearLayout (Vertical)` un widget de la **boîte à outils** vers le concepteur. Notez qu’un contour bleu indique les limites de l’ajout `LinearLayout`. La **structure du document** montre qu’il s’agit d' `LinearLayout`un enfant de `imageView1 (ImageView)`, situé sous :

[![Contour bleu](designer-walkthrough-images/vs/10-blue-outline-w158-sml.png)](designer-walkthrough-images/vs/10-blue-outline-w158.png#lightbox)

Lorsque vous sélectionnez `ImageView` dans le concepteur, le contour bleu se déplace pour entourer le `ImageView`. En outre, la sélection se déplace `imageView1 (ImageView)` vers dans la **structure du document**:

[![Sélectionner ImageView](designer-walkthrough-images/vs/11-select-imageview-w158-sml.png)](designer-walkthrough-images/vs/11-select-imageview-w158.png#lightbox)

Ensuite, faites glisser `Text (Large)` un widget de la **boîte à outils** vers le `LinearLayout`nouveau ajouté. Notez que le concepteur utilise des reflets verts pour indiquer l’emplacement où le nouveau widget sera inséré :

[![Points forts verts](designer-walkthrough-images/vs/12-green-highlight-w158-sml.png)](designer-walkthrough-images/vs/12-green-highlight-w158.png#lightbox)

Ensuite, ajoutez un `Text (Small)` widget sous le `Text (Large)` widget :

[![Ajouter un widget de texte de petite taille](designer-walkthrough-images/vs/13-add-small-text-w158-sml.png)](designer-walkthrough-images/vs/13-add-small-text-w158.png#lightbox)

À ce stade, l’aire du concepteur doit ressembler à la capture d’écran suivante :

[![Disposition du concepteur](designer-walkthrough-images/vs/14-raw-layout-w158-sml.png)](designer-walkthrough-images/vs/14-raw-layout-w158.png#lightbox)

Si les deux `textView` widgets ne sont pas `linearLayout1`à l’intérieur, vous pouvez `linearLayout1` les faire glisser vers dans la **structure du document** et les positionner de sorte qu’ils apparaissent comme indiqué dans `linearLayout1`la capture d’écran précédente (en retrait sous).

### <a name="arranging-the-user-interface"></a>Organisation de l’interface utilisateur

L’étape suivante consiste à modifier l’interface utilisateur pour afficher `ImageView` le à gauche, avec les deux `TextView` widgets empilés `ImageView`à droite de.

1. Sélectionnez le contrôle `ImageView`.

2. Dans la **fenêtre Propriétés**, entrez *Width* dans la zone de recherche et localisez la largeur de la **disposition**.

3. Modifiez le paramètre de **largeur** de `wrap_content`disposition en :

![Définir le contenu du retour à la ligne](designer-walkthrough-images/vs/15-wrap-content-w158.png)

Pour modifier le `Width` paramètre, vous pouvez également cliquer sur le triangle situé sur le côté droit du widget pour basculer le paramètre de `wrap_content`largeur sur :

![Faire glisser pour définir la largeur](designer-walkthrough-images/vs/15b-width-arrow-w158.png)

Le fait de cliquer à nouveau `Width` sur le `match_parent`triangle retourne le paramètre à. Ensuite, accédez au volet **structure du document** et sélectionnez la racine `LinearLayout`:

[![Sélectionner la racine élément LinearLayout](designer-walkthrough-images/vs/16-root-linearlayout-w158-sml.png)](designer-walkthrough-images/vs/16-root-linearlayout-w158.png#lightbox)

Une fois la `LinearLayout` racine sélectionnée, revenez au volet **Propriétés** , entrez *orientation* dans la zone de recherche et recherchez le paramètre **orientation** . Changer l’orientation `horizontal`en :

![Sélectionner l’orientation horizontale](designer-walkthrough-images/vs/17-horizontal-orientation-w158.png)

À ce stade, l’aire du concepteur doit ressembler à la capture d’écran suivante.
Notez que les `TextView` widgets ont été déplacés à droite `ImageView`du :

[![Disposition du concepteur](designer-walkthrough-images/vs/18-designer-layout-w158-sml.png)](designer-walkthrough-images/vs/18-designer-layout-w158.png#lightbox)

### <a name="modifying-the-spacing"></a>Modification de l’espacement

L’étape suivante consiste à modifier les paramètres de marge intérieure et de marge dans l’interface utilisateur pour fournir davantage d’espace entre les widgets. `ImageView` Sélectionnez dans l’aire de conception. Dans le volet **Propriétés** , entrez `min` dans la zone de recherche. Entrer `70dp` pour la **hauteur minimale** et `50dp` pour la **largeur min**. :

[![Définir la hauteur et la largeur](designer-walkthrough-images/vs/18b-set-height-width-sml.png)](designer-walkthrough-images/vs/18b-set-height-width.png#lightbox)

Dans le volet **Propriétés** , entrez `padding` dans la zone de recherche et `10dp` entrez pour le **remplissage**. Celles `minHeight`- `minWidth` ci `padding` , ainsi que les paramètres, ajoutent des `ImageView` marges autour de tous les côtés de la et s’allongent verticalement. Notez que la disposition XML change à mesure que vous entrez les valeurs suivantes :

[![Définir le remplissage](designer-walkthrough-images/vs/19-padding-widths-w158-sml.png)](designer-walkthrough-images/vs/19-padding-widths-w158.png#lightbox)

Les paramètres de marge intérieure inférieure, gauche, droite et supérieure peuvent être définis indépendamment en entrant des valeurs dans les champs **marge intérieure inférieure**, **marge intérieure gauche**, **marge intérieure droite**et **marge intérieure** , respectivement.
Par exemple, affectez au champ de `5dp` **marge intérieure** la valeur et aux champs **marge inférieure**, marge **intérieure droite**et **marge** intérieure de `10dp`remplissage :

[![Paramètres de marge intérieure personnalisés](designer-walkthrough-images/vs/20-custom-padding-w158-sml.png)](designer-walkthrough-images/vs/20-custom-padding-w158.png#lightbox)

Ensuite, ajustez la position du `LinearLayout` widget qui contient les deux `TextView` widgets. Dans la **structure du document**, `linearLayout1`sélectionnez. Dans la fenêtre **Propriétés** , entrez `margin` dans la zone de recherche. Définissez les marges **en bas**, marge de disposition à **gauche**et marge `5dp`de **disposition en haut** de. Définir la marge de disposition `0dp`à **droite** :

[![Définir les marges](designer-walkthrough-images/vs/21-margins-w158-sml.png)](designer-walkthrough-images/vs/21-margins-w158.png#lightbox)

### <a name="removing-the-default-image"></a>Suppression de l’image par défaut

Étant donné que est utilisé pour afficher les couleurs (plutôt que les images), l’étape suivante consiste à supprimer la source d’image par défaut ajoutée par le modèle. `ImageView`

1. Sélectionnez dans l' **aire du concepteur.** `ImageView`

2. Dans **Propriétés**, entrez *src* dans la zone de recherche.

3. Cliquez sur le petit carré situé à droite du paramètre de propriété **src** , puis sélectionnez **Réinitialiser**:

[![Effacer le paramètre SRC ImageView](designer-walkthrough-images/vs/22-clear-img-src-w158-sml.png)](designer-walkthrough-images/vs/22-clear-img-src-w158.png#lightbox)

Cela supprime `android:src="@android:drawable/ic_menu_gallery"` du code XML source pour cela `ImageView`.

### <a name="adding-a-listview-container"></a>Ajout d’un conteneur ListView

Maintenant que la disposition **list_item** est définie, l’étape suivante consiste à ajouter un `ListView` à la disposition principale. Elle `ListView` contient une liste de **list_item**. 

Dans la **Explorateur de solutions**, ouvrez **ressources/mise en page/activity_main. AXML**. Dans la **boîte à outils**, `ListView` Localisez le widget et faites-le glisser sur le **aire de conception**. Le `ListView` dans le concepteur sera vide, à l’exception des lignes bleues qui décrivent sa bordure lorsqu’il est sélectionné. Vous pouvez afficher la **structure du document** pour vérifier que le **ListView** a été ajouté correctement :

[![Nouveau ListView](designer-walkthrough-images/vs/23-new-listview-w158-sml.png)](designer-walkthrough-images/vs/23-new-listview-w158.png#lightbox)

Par défaut, la `ListView` `Id` valeur `@+id/listView1`est affectée à.
Quand est toujours sélectionné dans la structure du document, ouvrez le volet Propriétés, cliquez sur Réorganiser par, puis sélectionnez catégorie. `listView1`
Ouvrez **main**, localisez la propriété **ID** , puis remplacez sa valeur `@+id/myListView`par :

[![Renommer l’ID en myListView](designer-walkthrough-images/vs/24-change-id-w158-sml.png)](designer-walkthrough-images/vs/24-change-id-w158.png#lightbox)

À ce stade, l’interface utilisateur est prête à être utilisée.

### <a name="running-the-application"></a>Exécution de l’application

Ouvrez **MainActivity.cs** et remplacez son code par ce qui suit :

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using Android.Support.V7.App;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
    public class MainActivity : AppCompatActivity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.activity_main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}

```

Ce code utilise un adaptateur `ListView` personnalisé pour charger les informations de couleur et afficher ces données dans l’interface utilisateur qui vient d’être créée. Pour que cet exemple soit concis, les informations de couleur sont codées en dur dans une liste, mais l’adaptateur peut être modifié pour extraire les informations de couleur d’une source de données ou pour les calculer à la volée. Pour plus d’informations `ListView` sur les adaptateurs, consultez [ListView](~/android/user-interface/layouts/list-view/index.md).

Générez et exécutez l’application. La capture d’écran suivante est un exemple de la façon dont l’application s’affiche lors de l’exécution sur un appareil :

[![Capture d’écran finale](designer-walkthrough-images/vs/25-final-screenshot-sml.png)](designer-walkthrough-images/vs/25-final-screenshot.png#lightbox)

# <a name="visual-studio-for-mactabmacos"></a>[Visual Studio pour Mac](#tab/macos)

### <a name="creating-a-new-project"></a>Création d’un projet

La première étape consiste à créer un nouveau projet Xamarin. Android.

Lancez Visual Studio pour Mac puis cliquez sur **nouveau projet...** . Choisissez le modèle **application Android** , puis cliquez sur **suivant**:

[![Application vide Android](designer-walkthrough-images/xs/01-android-app-m75-sml.png)](designer-walkthrough-images/xs/01-android-app-m75.png#lightbox)

Nommez la nouvelle application **DesignerWalkthrough**. Sous **plateformes cibles**, sélectionnez la **plus récente et la plus grande** , puis cliquez sur **suivant**:

[![Nommer l’application](designer-walkthrough-images/xs/02-designer-walkthrough-m75-sml.png)](designer-walkthrough-images/xs/02-designer-walkthrough-m75.png#lightbox)

Dans l’écran suivant de la boîte de dialogue, cliquez sur **créer**.

### <a name="adding-a-layout"></a>Ajout d’une disposition

L’étape suivante consiste à créer un **élément LinearLayout** qui contiendra les éléments de l’interface utilisateur.

Dans Visual Studio pour Mac, cliquez avec le bouton droit sur **ressources/mise en page** dans le panneau **solutions** , puis sélectionnez **Ajouter > nouveau fichier...** . Dans la boîte de dialogue **nouveau fichier** , sélectionnez **Android > Layout**. Nommez le fichier **list_item** , puis cliquez sur **nouveau**:

[![Nouvelle disposition](designer-walkthrough-images/xs/03-new-layout-m75-sml.png)](designer-walkthrough-images/xs/03-new-layout-m75.png#lightbox)

Une fois ce fichier ajouté, la nouvelle disposition **list_item** s’affiche sur la **aire de conception** (si vous voyez le message, *ce projet contient des ressources qui n’ont pas été compilées avec succès, le rendu peut être affecté*, cliquez sur **Générer > Tout générer** pour générer le projet) :

[![Mode concepteur](designer-walkthrough-images/xs/04-designer-view-m75-sml.png)](designer-walkthrough-images/xs/04-designer-view-m75.png#lightbox)

Cliquez sur l’onglet **source** au bas du concepteur pour afficher la source XML de cette disposition. Lorsque vous cliquez sur l’onglet **structure du document** à droite, il indique que la disposition contient actuellement un widget **élément LinearLayout** unique :

[![XML du concepteur](designer-walkthrough-images/xs/05-designer-xml-m75-sml.png)](designer-walkthrough-images/xs/05-designer-xml-m75.png#lightbox)

L’étape suivante consiste à créer l’interface utilisateur pour l’application de l’Explorateur de couleurs.

### <a name="creating-the-list-item-user-interface"></a>Création de l’interface utilisateur de l’élément de liste

Cliquez sur l’onglet **Concepteur** en bas de l’écran pour revenir à l' **aire du concepteur**. Dans le volet **boîte à outils** à droite, faites défiler la liste jusqu’à la `ImageView`section **images & média** et recherchez :

[![Localiser ImageView](designer-walkthrough-images/xs/06-locate-imageview-m75-sml.png)](designer-walkthrough-images/xs/06-locate-imageview-m75.png#lightbox)

Vous pouvez également entrer *ImageView* dans la barre de recherche pour rechercher les `ImageView`éléments suivants :

[![Recherche ImageView](designer-walkthrough-images/xs/07-imageview-search-m75-sml.png)](designer-walkthrough-images/xs/07-imageview-search-m75.png#lightbox)

Faites-le glisser `ImageView` surleairedeconception(ilserautilisépourafficherunéchantillondecouleurdansl’applicationdel’Explorateurdecouleurs`ImageView` ) :

[![ImageView sur Canvas](designer-walkthrough-images/xs/08-imageview-on-canvas-m75-sml.png)](designer-walkthrough-images/xs/08-imageview-on-canvas-m75.png#lightbox)

Ensuite, faites glisser `LinearLayout (Vertical)` un widget de la **boîte à outils** vers le **aire de conception**. Notez qu’un contour bleu indique les limites de l’ajout `LinearLayout`. La **structure du document** montre qu’il s’agit d' `LinearLayout`un enfant de `imageView1 (ImageView)`, situé ci-dessous :

[![Contour bleu](designer-walkthrough-images/xs/10-blue-outline-m75-sml.png)](designer-walkthrough-images/xs/10-blue-outline-m75.png#lightbox)

Lorsque vous sélectionnez `ImageView` dans le concepteur, le contour bleu se déplace pour entourer le `ImageView`. En outre, la sélection se déplace `imageView1 (ImageView)` vers dans la **structure du document**:

[![Sélectionner ImageView](designer-walkthrough-images/xs/11-select-imageview-m75-sml.png)](designer-walkthrough-images/xs/11-select-imageview-m75.png#lightbox)

Ensuite, faites glisser `Text (Large)` un widget de la **boîte à outils** vers le `LinearLayout`nouveau ajouté. Notez que lorsque vous faites glisser la souris sur la **aire de conception**, elle met en surbrillance où le nouveau widget sera inséré.
Le `Text (Large)` widget doit se trouver à `linearLayout1` l’intérieur, comme indiqué ici :

[![Ajout du widget Text (Large)](designer-walkthrough-images/xs/12-green-highlight-m75-sml.png)](designer-walkthrough-images/xs/12-green-highlight-m75.png#lightbox)

Ensuite, ajoutez un `Text (Small)` widget sous le `Text (Large)` widget. À ce stade, le **aire de conception** doit ressembler à la capture d’écran suivante :

[![Ajouter un widget de texte de petite taille](designer-walkthrough-images/xs/13-add-small-text-m75-sml.png)](designer-walkthrough-images/xs/13-add-small-text-m75.png#lightbox)

Si les deux `textView` widgets ne sont pas `linearLayout1`à l’intérieur, vous pouvez `linearLayout1` les faire glisser vers dans la **structure du document** et les positionner pour qu’ils s’affichent comme indiqué dans `linearLayout1`la capture d’écran précédente (en retrait sous).

### <a name="arranging-the-user-interface"></a>Organisation de l’interface utilisateur

L’étape suivante consiste à modifier l’interface utilisateur pour afficher `ImageView` le à gauche, avec les deux `TextView` widgets empilés `ImageView`à droite de.

1. Avec le `ImageView` sélectionné, cliquez sur l’onglet **Propriétés** .

2. Juste en dessous de l’onglet **Propriétés** , cliquez sur **disposition**.

3. Faites défiler jusqu’à **ViewGroup** et `Width` remplacez `wrap_content`le paramètre par :

[![Définir le contenu du retour à la ligne](designer-walkthrough-images/xs/15-wrap-content-m75-sml.png)](designer-walkthrough-images/xs/15-wrap-content-m75.png#lightbox)

Pour modifier le `Width` paramètre, vous pouvez également cliquer sur le triangle situé sur le côté droit du widget pour basculer le paramètre de `wrap_content`largeur sur :

[![Faire glisser pour définir la largeur](designer-walkthrough-images/xs/16-width-arrow-m75-sml.png)](designer-walkthrough-images/xs/16-width-arrow-m75.png#lightbox)

Le fait de cliquer à nouveau `Width` sur le `match_parent`triangle retourne le paramètre à. Ensuite, accédez au volet **structure du document** et sélectionnez la racine `LinearLayout`:

[![Sélectionner la racine élément LinearLayout](designer-walkthrough-images/xs/17-root-linearlayout-m75-sml.png)](designer-walkthrough-images/xs/17-root-linearlayout-m75.png#lightbox)

Une fois la `LinearLayout` racine sélectionnée, revenez à l’onglet **Propriétés** et cliquez sur **widget**. Remplacez le `Orientation` `horizontal` paramètre par comme indiqué ci-dessous. À ce stade, le **aire de conception** doit ressembler à la capture d’écran suivante. Notez que les `TextView` widgets ont été déplacés à droite `ImageView`du :

[![Sélectionner l’orientation horizontale](designer-walkthrough-images/xs/18-horizontal-orientation-m75-sml.png)](designer-walkthrough-images/xs/18-horizontal-orientation-m75.png#lightbox)

### <a name="modifying-the-spacing"></a>Modification de l’espacement

L’étape suivante consiste à modifier les paramètres de marge intérieure et de marge dans l’interface utilisateur pour fournir davantage d’espace entre les widgets. Sélectionnez le `ImageView` , puis cliquez sur l’onglet **disposition** sous **Propriétés**. Remplacez la `Min Width` valeur `50dp`de par `Min Height` , `70dp` et`Padding` par. `10dp`
Cela applique le remplissage autour de tous les côtés `ImageView` de et l’allonge verticalement :

[![Définir le remplissage](designer-walkthrough-images/xs/20-padding-widths-m75-sml.png)](designer-walkthrough-images/xs/20-padding-widths-m75.png#lightbox)

Les paramètres de marge intérieure supérieure, droite, inférieure et gauche peuvent être définis indépendamment en entrant des valeurs dans `Top`les `Right`champs `Bottom`,, `Left` et de remplissage, respectivement. Par exemple, affectez `Left` à la valeur de `5dp` remplissage la `Top`valeur `Right`et aux `Bottom` valeurs de remplissage, `10dp`et à. Notez que le `Padding` paramètre passe à une liste de valeurs séparées par des virgules :

[![Paramètres de marge intérieure personnalisés](designer-walkthrough-images/xs/21-custom-padding-m75-sml.png)](designer-walkthrough-images/xs/21-custom-padding-m75.png#lightbox)

Ensuite, ajustez la position du `LinearLayout` widget qui contient les deux `TextView` widgets. Dans la **structure du document**, `linearLayout1`sélectionnez. Dans le volet **Propriétés** , sélectionnez l’onglet **disposition** . Faites défiler jusqu’à la section ViewGroup `Left`et `Top`définissez `Right`les `Bottom` marges, `5dp`, et `0dp`sur, `5dp` `5dp`, et respectivement :

[![Définir les marges](designer-walkthrough-images/xs/22-margins-m75-sml.png)](designer-walkthrough-images/xs/22-margins-m75.png#lightbox)

### <a name="removing-the-default-image"></a>Suppression de l’image par défaut

Étant donné que est utilisé pour afficher les couleurs (plutôt que les images), l’étape suivante consiste à supprimer la source d’image par défaut ajoutée par le modèle. `ImageView`

1. Sélectionnez le contrôle `ImageView`.

2. Sous **Propriétés**, cliquez sur l’onglet **widget** .

3. Effacez `Src` le paramètre pour qu’il soit vide :

[![Effacer le paramètre SRC ImageView](designer-walkthrough-images/xs/23-clear-src-m75-sml.png)](designer-walkthrough-images/xs/23-clear-src-m75.png#lightbox)

Cela supprime `android:src="@android:drawable/ic_menu_gallery"` du code XML source pour cela `ImageView`.

### <a name="adding-a-listview-container"></a>Ajout d’un conteneur ListView

Maintenant que la disposition **list_item** est définie, l’étape suivante consiste à ajouter un `ListView` à la disposition principale. Elle `ListView` contient une liste de **list_item**. 

Dans la **Explorateur de solutions**, ouvrez **ressources/mise en page/main. AXML**.
Cliquez sur `Button` le widget (le cas échéant) et supprimez-le. Dans la **boîte à outils**, `ListView` Localisez le widget et faites-le glisser sur le **aire de conception**.
Le `ListView` dans le concepteur sera vide, à l’exception des lignes bleues qui décrivent sa bordure lorsqu’il est sélectionné. Vous pouvez afficher la **structure du document** pour vérifier que le **ListView** a été ajouté correctement :

[![Nouveau ListView](designer-walkthrough-images/xs/24-new-listview-m75-sml.png)](designer-walkthrough-images/xs/24-new-listview-m75.png#lightbox)

Par défaut, la `ListView` `Id` valeur `@+id/listView1`est affectée à.
Quand est toujours sélectionné dans la structure du document, ouvrez le volet Propriétés, cliquez sur Réorganiser par, puis sélectionnez catégorie. `listView1`
Ouvrez **main**, localisez la propriété **ID** , puis remplacez sa valeur `@+id/myListView`par :

[![Renommer l’ID en myListView](designer-walkthrough-images/xs/25-change-id-m75-sml.png)](designer-walkthrough-images/xs/25-change-id-m75.png#lightbox)

À ce stade, l’interface utilisateur est prête à être utilisée.

### <a name="running-the-application"></a>Exécution de l’application

Ouvrez **MainActivity.cs** et remplacez son code par ce qui suit :

```csharp
using Android.App;
using Android.Widget;
using Android.Views;
using Android.OS;
using System.Collections.Generic;

namespace DesignerWalkthrough
{
    [Activity(Label = "@string/app_name", MainLauncher = true)]
    public class MainActivity : Activity
    {
        List<ColorItem> colorItems = new List<ColorItem>();
        ListView listView;

        protected override void OnCreate(Bundle savedInstanceState)
        {
            base.OnCreate(savedInstanceState);

            // Set our view from the "main" layout resource
            SetContentView(Resource.Layout.Main);
            listView = FindViewById<ListView>(Resource.Id.myListView);

            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.DarkRed,
                ColorName = "Dark Red",
                Code = "8B0000"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.SlateBlue,
                ColorName = "Slate Blue",
                Code = "6A5ACD"
            });
            colorItems.Add(new ColorItem()
            {
                Color = Android.Graphics.Color.ForestGreen,
                ColorName = "Forest Green",
                Code = "228B22"
            });

            listView.Adapter = new ColorAdapter(this, colorItems);
        }
    }

    public class ColorAdapter : BaseAdapter<ColorItem>
    {
        List<ColorItem> items;
        Activity context;
        public ColorAdapter(Activity context, List<ColorItem> items)
            : base()
        {
            this.context = context;
            this.items = items;
        }
        public override long GetItemId(int position)
        {
            return position;
        }
        public override ColorItem this[int position]
        {
            get { return items[position]; }
        }
        public override int Count
        {
            get { return items.Count; }
        }
        public override View GetView(int position, View convertView, ViewGroup parent)
        {
            var item = items[position];

            View view = convertView;
            if (view == null) // no view to re-use, create new
                view = context.LayoutInflater.Inflate(Resource.Layout.list_item, null);
            view.FindViewById<TextView>(Resource.Id.textView1).Text = item.ColorName;
            view.FindViewById<TextView>(Resource.Id.textView2).Text = item.Code;
            view.FindViewById<ImageView>(Resource.Id.imageView1).SetBackgroundColor(item.Color);

            return view;
        }
    }

    public class ColorItem
    {
        public string ColorName { get; set; }
        public string Code { get; set; }
        public Android.Graphics.Color Color { get; set; }
    }
}
```

Ce code utilise un adaptateur `ListView` personnalisé pour charger les informations de couleur et afficher ces données dans l’interface utilisateur qui vient d’être créée. Pour que cet exemple soit concis, les informations de couleur sont codées en dur dans une liste, mais l’adaptateur peut être modifié pour extraire les informations de couleur d’une source de données ou pour les calculer à la volée. Pour plus d’informations `ListView` sur les adaptateurs, consultez [ListView](~/android/user-interface/layouts/list-view/index.md).

Générez et exécutez l’application. La capture d’écran suivante est un exemple de la façon dont l’application s’affiche lors de l’exécution sur un appareil :

[![Capture d’écran finale](designer-walkthrough-images/xs/26-final-screenshot-sml.png)](designer-walkthrough-images/xs/26-final-screenshot.png#lightbox)

-----

## <a name="summary"></a>Récapitulatif

Cet article a décrit le processus d’utilisation de Xamarin. Android Designer dans Visual Studio pour créer une interface utilisateur pour une application de base.
Il a démontré comment créer l’interface pour un élément unique dans une liste, et illustre comment ajouter des widgets et les disposer visuellement.
Elle a également expliqué comment affecter des ressources, puis définir diverses propriétés sur ces widgets.
