---
titre : « afficher les fenêtres contextuelles » Description : Xamarin.Forms fournit trois éléments d’interface utilisateur de type popup : une alerte, une feuille d’action et une invite. Cet article explique comment utiliser les API d’alerte, de feuille d’action et d’invite pour afficher des boîtes de dialogue qui demandent aux utilisateurs des questions simples, guident les utilisateurs à travers des tâches et affichent des invites.»
ms. Prod : xamarin ms. AssetID : 46AB0D5E-0025-4A8A-9D00-3E66C3D0BA2E ms. Technology : xamarin-Forms Author : davidbritch ms. Author : dabritch ms. Date : 03/10/2020 No-Loc : [ Xamarin.Forms , Xamarin.Essentials ]
---

# <a name="display-pop-ups"></a>Afficher les fenêtres contextuelles

[![Télécharger ](~/media/shared/download.png) l’exemple télécharger l’exemple](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)

L’affichage d’une alerte, la demande d’un utilisateur ou l’affichage d’une invite est une tâche courante de l’interface utilisateur. Xamarin.Formsa trois méthodes sur la [`Page`](xref:Xamarin.Forms.Page) classe pour interagir avec l’utilisateur via une fenêtre contextuelle : [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) , [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) et `DisplayPromptAsync` . Elles sont affichées avec les contrôles natifs appropriés sur chaque plateforme.

## <a name="display-an-alert"></a>Afficher une alerte

Toutes les Xamarin.Forms plateformes prises en charge disposent d’une fenêtre contextuelle modale pour avertir l’utilisateur ou poser des questions simples. Pour afficher ces alertes dans Xamarin.Forms , utilisez la [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode sur Any [`Page`](xref:Xamarin.Forms.Page) . La ligne de code suivante présente un message simple à l’utilisateur :

```csharp
await DisplayAlert ("Alert", "You have been alerted", "OK");
```

![](pop-ups-images/alert.png "Alert Dialog with One Button")

Cet exemple ne recueille aucune information de l’utilisateur. L’alerte s’affiche de façon modale, et une fois qu’elle est fermée l’utilisateur continue à interagir avec l’application.

La [`DisplayAlert`](xref:Xamarin.Forms.Page.DisplayAlert*) méthode peut également être utilisée pour capturer la réponse d’un utilisateur en présentant deux boutons et en retournant un `boolean` . Pour obtenir une réponse à partir d’une alerte, fournissez du texte pour les deux boutons et attendez (`await`) la méthode. Une fois que l’utilisateur a sélectionné l’une des options, la réponse est retournée à votre code. Notez les mots clés `async` et `await` dans l’exemple de code ci-dessous :

```csharp
async void OnAlertYesNoClicked (object sender, EventArgs e)
{
  bool answer = await DisplayAlert ("Question?", "Would you like to play a game", "Yes", "No");
  Debug.WriteLine ("Answer: " + answer);
}
```

[![DisplayAlert](pop-ups-images/alert2-sml.png "Boîte de dialogue d’alerte avec deux boutons")](pop-ups-images/alert2.png#lightbox "Boîte de dialogue d’alerte avec deux boutons")

## <a name="guide-users-through-tasks"></a>Guider les utilisateurs à travers les tâches

[UIActionSheet](https://developer.apple.com/library/ios/documentation/uikit/reference/uiactionsheet_class/Reference/Reference.html) est un élément d’interface utilisateur courant dans iOS. La Xamarin.Forms [`DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) méthode vous permet d’inclure ce contrôle dans des applications multiplateformes, en rendant les alternatives natives dans Android et UWP.

Pour afficher une feuille d’action, attendez (`await` [) `DisplayActionSheet`](xref:Xamarin.Forms.Page.DisplayActionSheet*) dans n’importe quelle [`Page`](xref:Xamarin.Forms.Page), en passant les étiquettes de message et de bouton sous forme de chaînes. La méthode retourne l’étiquette de chaîne du bouton sur lequel l’utilisateur a cliqué. Voici un exemple simple :

```csharp
async void OnActionSheetSimpleClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: Send to?", "Cancel", null, "Email", "Twitter", "Facebook");
  Debug.WriteLine ("Action: " + action);
}
```

![](pop-ups-images/action.png "ActionSheet Dialog")

Le bouton `destroy` est restitué différemment des autres et peut être laissé `null` ou spécifié en tant que troisième paramètre de chaîne. L’exemple suivant utilise le bouton `destroy` :

```csharp
async void OnActionSheetCancelDeleteClicked (object sender, EventArgs e)
{
  string action = await DisplayActionSheet ("ActionSheet: SavePhoto?", "Cancel", "Delete", "Photo Roll", "Email");
  Debug.WriteLine ("Action: " + action);
}
```

[![DisplayActionSheet](pop-ups-images/action2-sml.png "Boîte de dialogue de la feuille d’action avec le bouton détruire")](pop-ups-images/action2.png#lightbox "Boîte de dialogue de la feuille d’action avec le bouton détruire")

## <a name="display-a-prompt"></a>Afficher une invite

Pour afficher une invite, appelez `DisplayPromptAsync` dans Any [`Page`](xref:Xamarin.Forms.Page) , en passant un titre et un message comme `string` arguments :

```csharp
string result = await DisplayPromptAsync("Question 1", "What's your name?");
```

L’invite s’affiche de façon modale :

[![Capture d’écran d’une invite modale, sur iOS et Android](pop-ups-images/simple-prompt.png "Invite modale")](pop-ups-images/simple-prompt-large.png#lightbox "Invite modale")

Si vous avez cliqué sur le bouton OK, la réponse entrée est retournée en tant que `string` . Si vous avez cliqué sur le bouton Annuler, `null` est retourné.

La liste complète des arguments pour la `DisplayPromptAsync` méthode est la suivante :

- `title`, de type `string` , est le titre à afficher dans l’invite de commandes.
- `message`, de type `string` , est le message à afficher dans l’invite de commandes.
- `accept`, de type `string` , est le texte du bouton accepter. Il s’agit d’un argument facultatif, dont la valeur par défaut est OK.
- `cancel`, de type `string` , est le texte du bouton Annuler. Il s’agit d’un argument facultatif, dont la valeur par défaut est Cancel.
- `placeholder`, de type `string` , est le texte d’espace réservé à afficher dans l’invite de commandes. Il s’agit d’un argument facultatif, dont la valeur par défaut est `null` .
- `maxLength`, de type `int` , est la longueur maximale de la réponse de l’utilisateur. Il s’agit d’un argument facultatif, dont la valeur par défaut est-1.
- `keyboard`, de type `Keyboard` , est le type de clavier à utiliser pour la réponse de l’utilisateur. Il s’agit d’un argument facultatif, dont la valeur par défaut est `Keyboard.Default` .
- `initialValue`, de type `string` , est une réponse prédéfinie qui s’affiche et qui peut être modifiée. Il s’agit d’un argument facultatif, dont la valeur par défaut est un vide `string` .

L’exemple suivant illustre la définition de certains des arguments facultatifs :

```csharp
string result = await DisplayPromptAsync("Question 2", "What's 5 + 5?", initialValue: "10", maxLength: 2, keyboard: Keyboard.Numeric);
```

Ce code affiche une réponse prédéfinie de 10, limite le nombre de caractères pouvant être entrés à 2, et affiche le clavier numérique pour les entrées utilisateur :

[![Capture d’écran d’une invite modale, sur iOS et Android](pop-ups-images/keyboard-prompt.png "Invite modale")](pop-ups-images/keyboard-prompt-large.png#lightbox "Invite modale")

## <a name="related-links"></a>Liens connexes

- [PopupsSample](https://docs.microsoft.com/samples/xamarin/xamarin-forms-samples/navigation-pop-ups)
