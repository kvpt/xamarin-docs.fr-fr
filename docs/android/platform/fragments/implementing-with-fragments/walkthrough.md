---
title: Xamarin.Android Fragments Procédure pas à pas - Partie 1
ms.prod: xamarin
ms.topic: tutorial
ms.assetid: ED368FA9-A34E-DC39-D535-5C34C32B9761
ms.technology: xamarin-android
author: davidortinau
ms.author: daortin
ms.date: 08/21/2018
ms.openlocfilehash: 043ad02f9ca9148910364ac82917551ee58d72ba
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "73027403"
---
# <a name="fragments-walkthrough-ndash-phone"></a>Fragments téléphone pas &ndash; à pas

C’est la première partie d’une procédure pas à pas qui va créer une application Xamarin.Android qui cible un appareil Android dans l’orientation portrait. Cette procédure pas à pas discutera de la façon de créer des fragments dans Xamarin.Android et comment les ajouter à un échantillon.

[![](./images/intro-screenshot-phone-sml.png)](./images/intro-screenshot-phone.png#lightbox)

Les classes suivantes seront créées pour cette application :

1. `PlayQuoteFragment`&nbsp; Ce fragment affichera une citation d’une pièce de William Shakespeare. Il sera accueilli `PlayQuoteActivity`par .
1. `Shakespeare`&nbsp; Cette classe sera tenue deux tableaux codés en dur comme propriétés.
1. `TitlesFragment`&nbsp; Ce fragment affichera une liste de titres de pièces qui ont été écrites par William Shakespeare. Il sera accueilli `MainActivity`par .
1. `PlayQuoteActivity`commencera la `PlayQuoteActivity` réponse à l’utilisateur sélectionnant un jeu dans `TitlesFragment`. &nbsp; `TitlesFragment`

## <a name="1-create-the-android-project"></a>1. Créer le projet Android

Créer un nouveau projet Xamarin.Android appelé **FragmentSample**.
# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Créer un projet Xamarin.Android](./walkthrough-images/01-newproject.w157-sml.png)](./walkthrough-images/01-newproject.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Création d’un nouveau projet Xamarin.Android](./walkthrough-images/01-newproject.m742-sml.png)](./walkthrough-images/01-newproject.m742.png#lightbox)

Il est recommandé de sélectionner **le développement moderne** pour cette procédure pas à pas.

Après la création du projet, renommer la disposition du fichier **/ Main.axml** à **layout/activity_main.axml**.

-----

## <a name="2-add-the-data"></a>2. Ajouter les données

Les données de cette application seront stockées dans deux tableaux de `Shakespeare`cordes codés en dur qui sont des propriétés d’un nom de classe :

* `Shakespeare.Titles`&nbsp; Ce tableau tiendra une liste de pièces de William Shakespeare. C’est la source `TitlesFragment`de données pour le .
* `Shakespeare.Dialogue`&nbsp; Ce tableau tiendra une liste de citations `Shakespeare.Titles`de l’une des pièces contenues dans . C’est la source `PlayQuoteFragment`de données pour le .

Ajoutez une nouvelle classe Cmd au projet **FragmentSample** et nommez-le **Shakespeare.cs**. À l’intérieur de ce fichier, créez une nouvelle classe Cmd appelée `Shakespeare` avec le contenu suivant

```csharp
class Shakespeare
{
    public static string[] Titles = {
                                      "Henry IV (1)",
                                      "Henry V",
                                      "Henry VIII",
                                      "Richard II",
                                      "Richard III",
                                      "Merchant of Venice",
                                      "Othello",
                                      "King Lear"
                                    };

    public static string[] Dialogue = {
                                        "So shaken as we are, so wan with care, Find we a time for frighted peace to pant, And breathe short-winded accents of new broils To be commenced in strands afar remote. No more the thirsty entrance of this soil Shall daub her lips with her own children's blood; Nor more shall trenching war channel her fields, Nor bruise her flowerets with the armed hoofs Of hostile paces: those opposed eyes, Which, like the meteors of a troubled heaven, All of one nature, of one substance bred, Did lately meet in the intestine shock And furious close of civil butchery Shall now, in mutual well-beseeming ranks, March all one way and be no more opposed Against acquaintance, kindred and allies: The edge of war, like an ill-sheathed knife, No more shall cut his master. Therefore, friends, As far as to the sepulchre of Christ, Whose soldier now, under whose blessed cross We are impressed and engaged to fight, Forthwith a power of English shall we levy; Whose arms were moulded in their mothers' womb To chase these pagans in those holy fields Over whose acres walk'd those blessed feet Which fourteen hundred years ago were nail'd For our advantage on the bitter cross. But this our purpose now is twelve month old, And bootless 'tis to tell you we will go: Therefore we meet not now. Then let me hear Of you, my gentle cousin Westmoreland, What yesternight our council did decree In forwarding this dear expedience.",
                                        "Hear him but reason in divinity, And all-admiring with an inward wish You would desire the king were made a prelate: Hear him debate of commonwealth affairs, You would say it hath been all in all his study: List his discourse of war, and you shall hear A fearful battle render'd you in music: Turn him to any cause of policy, The Gordian knot of it he will unloose, Familiar as his garter: that, when he speaks, The air, a charter'd libertine, is still, And the mute wonder lurketh in men's ears, To steal his sweet and honey'd sentences; So that the art and practic part of life Must be the mistress to this theoric: Which is a wonder how his grace should glean it, Since his addiction was to courses vain, His companies unletter'd, rude and shallow, His hours fill'd up with riots, banquets, sports, And never noted in him any study, Any retirement, any sequestration From open haunts and popularity.",
                                        "I come no more to make you laugh: things now, That bear a weighty and a serious brow, Sad, high, and working, full of state and woe, Such noble scenes as draw the eye to flow, We now present. Those that can pity, here May, if they think it well, let fall a tear; The subject will deserve it. Such as give Their money out of hope they may believe, May here find truth too. Those that come to see Only a show or two, and so agree The play may pass, if they be still and willing, I'll undertake may see away their shilling Richly in two short hours. Only they That come to hear a merry bawdy play, A noise of targets, or to see a fellow In a long motley coat guarded with yellow, Will be deceived; for, gentle hearers, know, To rank our chosen truth with such a show As fool and fight is, beside forfeiting Our own brains, and the opinion that we bring, To make that only true we now intend, Will leave us never an understanding friend. Therefore, for goodness' sake, and as you are known The first and happiest hearers of the town, Be sad, as we would make ye: think ye see The very persons of our noble story As they were living; think you see them great, And follow'd with the general throng and sweat Of thousand friends; then in a moment, see How soon this mightiness meets misery: And, if you can be merry then, I'll say A man may weep upon his wedding-day.",
                                        "First, heaven be the record to my speech! In the devotion of a subject's love, Tendering the precious safety of my prince, And free from other misbegotten hate, Come I appellant to this princely presence. Now, Thomas Mowbray, do I turn to thee, And mark my greeting well; for what I speak My body shall make good upon this earth, Or my divine soul answer it in heaven. Thou art a traitor and a miscreant, Too good to be so and too bad to live, Since the more fair and crystal is the sky, The uglier seem the clouds that in it fly. Once more, the more to aggravate the note, With a foul traitor's name stuff I thy throat; And wish, so please my sovereign, ere I move, What my tongue speaks my right drawn sword may prove.",
                                        "Now is the winter of our discontent Made glorious summer by this sun of York; And all the clouds that lour'd upon our house In the deep bosom of the ocean buried. Now are our brows bound with victorious wreaths; Our bruised arms hung up for monuments; Our stern alarums changed to merry meetings, Our dreadful marches to delightful measures. Grim-visaged war hath smooth'd his wrinkled front; And now, instead of mounting barded steeds To fright the souls of fearful adversaries, He capers nimbly in a lady's chamber To the lascivious pleasing of a lute. But I, that am not shaped for sportive tricks, Nor made to court an amorous looking-glass; I, that am rudely stamp'd, and want love's majesty To strut before a wanton ambling nymph; I, that am curtail'd of this fair proportion, Cheated of feature by dissembling nature, Deformed, unfinish'd, sent before my time Into this breathing world, scarce half made up, And that so lamely and unfashionable That dogs bark at me as I halt by them; Why, I, in this weak piping time of peace, Have no delight to pass away the time, Unless to spy my shadow in the sun And descant on mine own deformity: And therefore, since I cannot prove a lover, To entertain these fair well-spoken days, I am determined to prove a villain And hate the idle pleasures of these days. Plots have I laid, inductions dangerous, By drunken prophecies, libels and dreams, To set my brother Clarence and the king In deadly hate the one against the other: And if King Edward be as true and just As I am subtle, false and treacherous, This day should Clarence closely be mew'd up, About a prophecy, which says that 'G' Of Edward's heirs the murderer shall be. Dive, thoughts, down to my soul: here Clarence comes.",
                                        "To bait fish withal: if it will feed nothing else, it will feed my revenge. He hath disgraced me, and hindered me half a million; laughed at my losses, mocked at my gains, scorned my nation, thwarted my bargains, cooled my friends, heated mine enemies; and what's his reason? I am a Jew. Hath not a Jew eyes? hath not a Jew hands, organs, dimensions, senses, affections, passions? fed with the same food, hurt with the same weapons, subject to the same diseases, healed by the same means, warmed and cooled by the same winter and summer, as a Christian is? If you prick us, do we not bleed? if you tickle us, do we not laugh? if you poison us, do we not die? and if you wrong us, shall we not revenge? If we are like you in the rest, we will resemble you in that. If a Jew wrong a Christian, what is his humility? Revenge. If a Christian wrong a Jew, what should his sufferance be by Christian example? Why, revenge. The villany you teach me, I will execute, and it shall go hard but I will better the instruction.",
                                        "Virtue! a fig! 'tis in ourselves that we are thus or thus. Our bodies are our gardens, to the which our wills are gardeners: so that if we will plant nettles, or sow lettuce, set hyssop and weed up thyme, supply it with one gender of herbs, or distract it with many, either to have it sterile with idleness, or manured with industry, why, the power and corrigible authority of this lies in our wills. If the balance of our lives had not one scale of reason to poise another of sensuality, the blood and baseness of our natures would conduct us to most preposterous conclusions: but we have reason to cool our raging motions, our carnal stings, our unbitted lusts, whereof I take this that you call love to be a sect or scion.",
                                        "Blow, winds, and crack your cheeks! rage! blow! You cataracts and hurricanoes, spout Till you have drench'd our steeples, drown'd the cocks! You sulphurous and thought-executing fires, Vaunt-couriers to oak-cleaving thunderbolts, Singe my white head! And thou, all-shaking thunder, Smite flat the thick rotundity o' the world! Crack nature's moulds, an germens spill at once, That make ingrateful man!"
                                    };
}
```

## <a name="3-create-the-playquotefragment"></a>3. Créer le PlayQuoteFragment

Le `PlayQuoteFragment` est un fragment Android qui affichera un devis pour une pièce de Shakespeare qui a été sélectionné par l’utilisateur plus tôt dans l’application, Ce fragment n’utilisera pas un fichier de mise en page Android; au lieu de cela, il va créer dynamiquement son interface utilisateur. Ajouter une `Fragment` nouvelle `PlayQuoteFragment` classe nommée au projet :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Ajouter une nouvelle classe C](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/02-addclass.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Ajouter une nouvelle classe C](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/02-addclass.m742.png#lightbox)

-----

Ensuite, changer le code pour le fragment pour ressembler à cet extrait:

```csharp
public class PlayQuoteFragment : Fragment
{
    public int PlayId => Arguments.GetInt("current_play_id", 0);

    public static PlayQuoteFragment NewInstance(int playId)
    {
        var bundle = new Bundle();
        bundle.PutInt("current_play_id", playId);
        return new PlayQuoteFragment {Arguments = bundle};
    }

    public override View OnCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState)
    {
        if (container == null)
        {
            return null;
        }

        var textView = new TextView(Activity);
        var padding = Convert.ToInt32(TypedValue.ApplyDimension(ComplexUnitType.Dip, 4, Activity.Resources.DisplayMetrics));
        textView.SetPadding(padding, padding, padding, padding);
        textView.TextSize = 24;
        textView.Text = Shakespeare.Dialogue[PlayId];

        var scroller = new ScrollView(Activity);
        scroller.AddView(textView);

        return scroller;
    }
}
```

Il s’agit d’un modèle commun dans les applications Android pour fournir une méthode d’usine qui instantanément un fragment. Cela garantit que le fragment sera créé avec les paramètres nécessaires pour un bon fonctionnement. Dans cette procédure pas à pas, l’application devrait utiliser la `PlayQuoteFragment.NewInstance` méthode pour créer un nouveau fragment chaque fois qu’un devis est sélectionné. La `NewInstance` méthode prendra un &ndash; seul paramètre de l’index de la citation pour afficher.

La `OnCreateView` méthode sera invoquée par Android quand il est temps de rendre le fragment sur l’écran. Il retournera `View` un objet Android qui est le fragment. Ce fragment n’utilise pas de fichier de mise en page pour créer une vue. Au lieu de cela, il créera programmatiquement la vue en instantanéisant un **TextView** pour tenir la citation, et affichera ce widget dans un **ScrollView**.

> [!NOTE]
> Les sous-classes fragmentes doivent avoir un constructeur public par défaut qui n’a pas de paramètres.

## <a name="4-create-the-playquoteactivity"></a>4. Créer la PlayQuoteActivity

Fragments doivent être hébergés à l’intérieur d’une `PlayQuoteFragment`activité, de sorte que cette application nécessite une activité qui accueillera le . L’activité ajoutera dynamiquement le fragment à sa disposition au moment de la course. Ajoutez une nouvelle activité à `PlayQuoteActivity`l’application et nommez-la :

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Ajouter l’activité Android au projet](./walkthrough-images/03-addactivity.w157-sml.png)](./walkthrough-images/03-addactivity.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Ajouter l’activité Android au projet](./walkthrough-images/03-addactivity.m742-sml.png)](./walkthrough-images/03-addactivity.m742.png#lightbox)

-----

Modifier le `PlayQuoteActivity`code dans :

```csharp
[Activity(Label = "PlayQuoteActivity")]
public class PlayQuoteActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);

        var playId = Intent.Extras.GetInt("current_play_id", 0);

        var detailsFrag = PlayQuoteFragment.NewInstance(playId);
        FragmentManager.BeginTransaction()
                        .Add(Android.Resource.Id.Content, detailsFrag)
                        .Commit();
    }
}
```

Quand `PlayQuoteActivity` est créé, il instantanément `PlayQuoteFragment` un nouveau et charger ce fragment dans `FragmentTransaction`sa vue racine dans le contexte d’un . Notez que cette activité ne charge pas un fichier de mise en page Android pour son interface utilisateur. Au lieu `PlayQuoteFragment` de cela, une nouvelle est ajoutée à la vue racine de l’application. L’identifiant `Android.Resource.Id.Content` de ressource est utilisé pour désigner la vue racine d’une activité sans connaître son identifiant spécifique.

## <a name="5-create-titlesfragment"></a>5. Créer TitlesFragment

Le `TitlesFragment` sera sous-classe d’un fragment spécialisé connu sous le nom d’un `ListFragment` qui résume la logique pour afficher un `ListView` dans un fragment. A `ListFragment` expose `ListAdapter` une propriété (utilisée par le `ListView` pour afficher `OnListItemClick` son contenu) et un gestionnaire d’événement nommé `ListView`qui permet au fragment de répondre aux clics sur une ligne qui est affiché par le .

Pour commencer, ajoutez un nouveau fragment au projet et **nommez-le TitlesFragment**:

# <a name="visual-studio"></a>[Visual Studio](#tab/windows)

[![Ajouter le fragment Android au projet](./walkthrough-images/04-addfragment.w157-sml.png)](./walkthrough-images/04-addfragment.w157.png#lightbox)

# <a name="visual-studio-for-mac"></a>[Visual Studio pour Mac](#tab/macos)

[![Ajouter le fragment Android au projet](./walkthrough-images/04-addfragment.m742-sml.png)](./walkthrough-images/04-addfragment.m742.png#lightbox)

-----

Modifier le code à l’intérieur du fragment :

```csharp
public class TitlesFragment : ListFragment
{
    int selectedPlayId;

    public TitlesFragment()
    {
        // Being explicit about the requirement for a default constructor.
    }

    public override void OnActivityCreated(Bundle savedInstanceState)
    {
        base.OnActivityCreated(savedInstanceState);
        ListAdapter = new ArrayAdapter<String>(Activity, Android.Resource.Layout.SimpleListItemActivated1, Shakespeare.Titles);

        if (savedInstanceState != null)
        {
            selectedPlayId = savedInstanceState.GetInt("current_play_id", 0);
        }
    }

    public override void OnSaveInstanceState(Bundle outState)
    {
        base.OnSaveInstanceState(outState);
        outState.PutInt("current_play_id", selectedPlayId);
    }

    public override void OnListItemClick(ListView l, View v, int position, long id)
    {
        ShowPlayQuote(position);
    }

    void ShowPlayQuote(int playId)
    {
        var intent = new Intent(Activity, typeof(PlayQuoteActivity));
        intent.PutExtra("current_play_id", playId);
        StartActivity(intent);
    }
}
```

Lorsque l’activité est créée `OnActivityCreated` Android invoque la méthode du fragment; c’est là que `ListView` l’adaptateur de liste pour le est créé.  La `ShowQuoteFromPlay` méthode commencera une `PlayQuoteActivity` instance de la pour afficher la citation pour la pièce sélectionnée.

## <a name="display-titlesfragment-in-mainactivity"></a>Afficher les titresFragment dans MainActivity

La dernière étape `TitlesFragment` est `MainActivity`d’afficher à l’intérieur . L’activité ne charge pas dynamiquement le fragment. Au lieu de cela, le fragment sera chargé statiquement en `fragment` le déclarant dans le fichier de mise en page de l’activité à l’aide d’un élément. Le fragment à charger est `android:name` identifié en définissant l’attribut à la classe de fragment (y compris l’espace nom du type). Par exemple, pour `TitlesFragment`utiliser `android:name` le , `FragmentSample.TitlesFragment`alors serait réglé à .

Modifier le fichier de mise en page **activity_main.axml**, en remplaçant le XML existant par ce qui suit:

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:orientation="horizontal"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <fragment
        android:name="FragmentSample.TitlesFragment"
        android:id="@+id/titles"
        android:layout_width="match_parent"
        android:layout_height="match_parent" />
</LinearLayout>
```

> [!NOTE]
> L’attribut `class` est un `android:name`substitut valide pour . Il n’y a pas d’orientation formelle sur le formulaire `class` qui `android:name`est préféré, il existe de nombreux exemples de bases de code qui utiliseront de manière interchangeable avec .

Aucune modification de code n’est requise pour MainActivity. Le code de cette classe devrait être très similaire à cet extrait :

```csharp
[Activity(Label = "@string/app_name", Theme = "@style/AppTheme", MainLauncher = true)]
public class MainActivity : Activity
{
    protected override void OnCreate(Bundle savedInstanceState)
    {
        base.OnCreate(savedInstanceState);
        SetContentView(Resource.Layout.activity_main);
    }
}
```

## <a name="run-the-app"></a>Exécuter l’application

Maintenant que le code est terminé, exécutez l’application sur un appareil pour le voir en action.

[![Captures d’écran de l’application en cours d’exécution sur un téléphone.](./walkthrough-images/05-app-screenshots-sml.png)](./walkthrough-images/05-app-screenshots.png#lightbox)

[La partie 2 de cette procédure pas à pas](./walkthrough-landscape.md) optimisera cette application pour les appareils fonctionnant en mode paysage.
