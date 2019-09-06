---
title: Utilisation de l’infrastructure de langage naturel avec Xamarin. iOS
description: Ce document décrit l’infrastructure de langage naturel. Introduite dans iOS 12, l’infrastructure de langage naturel est l’API iOS préférée à utiliser pour la reconnaissance de la langue, les parties de l’identification vocale et la reconnaissance d’entités nommées.
ms.prod: xamarin
ms.assetid: 126C8764-F873-4EB9-98A3-D82AB5689111
ms.technology: xamarin-ios
author: conceptdev
ms.author: crdun
ms.date: 08/20/2018
ms.openlocfilehash: 7b0d07f2153a0395146506a371631e0bcf75ebef
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70292909"
---
# <a name="using-the-natural-language-framework-with-xamarinios"></a>Utilisation de l’infrastructure de langage naturel avec Xamarin. iOS

Introduite dans iOS 12, l’infrastructure de langage naturel active le traitement du langage naturel sur l’appareil. Il prend en charge la reconnaissance linguistique, la création de jetons et le balisage. La création de jetons fractionne le texte en ses mots, phrases ou paragraphes de composants ; le balisage identifie des parties de la parole, des personnes, des lieux et des organisations.

L’infrastructure de langage naturel peut également utiliser des modèles de noyau ML personnalisés pour classer et baliser du texte dans des contextes spécialisés.

La classe [NSLinguisticTagger](xref:Foundation.NSLinguisticTagger) est toujours disponible. Toutefois, l’infrastructure de langage naturel est le mécanisme par défaut à utiliser pour le traitement en langage naturel.

## <a name="sample-app-xamarinnl"></a>Exemple d’application : XamarinNL

Pour savoir comment utiliser l’infrastructure de langage naturel avec Xamarin. iOS, jetez un coup d’œil sur l' [exemple d’application XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl).
Cet exemple d’application montre comment utiliser l’infrastructure de langage naturel pour :

- [Reconnaître les langues](#recognizing-languages).
- [Jetons du texte en mots et en phrases](#tokenizing-text-into-words-sentences-and-paragraphs).
- [Baliser les entités nommées et les parties de la parole](#tagging-named-entities-and-parts-of-speech).

## <a name="recognizing-languages"></a>Reconnaissance des langues

L’onglet module de **reconnaissance** de l’exemple d’application montre comment utiliser un[`NLLanguageRecognizer`](xref:NaturalLanguage.NLLanguageRecognizer)
pour déterminer la langue d’un bloc de texte.

> [!NOTE]
> La reconnaissance de la langue est un type spécifique de classification de texte. L’infrastructure de langage naturel prend également en charge la classification de texte personnalisée via les modèles ML de base fournis par les développeurs. Pour plus d’informations, consultez la session Présentation de l' [infrastructure de langage naturel](https://developer.apple.com/videos/play/wwdc2018/713/) à partir de WWDC 2018.

### <a name="dominant-language"></a>Langue dominante

Appuyez sur le bouton **langue** pour identifier la langue dominante dans l’entrée utilisateur.

La `HandleDetermineLanguageButtonTap` méthode`LanguageRecognizerViewController` de utilise l'[`GetDominantLanguage`](xref:NaturalLanguage.NLLanguageRecognizer.GetDominantLanguage*)
méthode d’un `NLLanguageRecognizer` pour extraire le[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
pour la langue principale trouvée dans le texte :

```csharp
partial void HandleDetermineLanguageButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        NLLanguage lang = NLLanguageRecognizer.GetDominantLanguage(UserInput.Text);
        DominantLanguageLabel.Text = lang.ToString();
    }
}
```

### <a name="language-probabilities"></a>Probabilités de la langue

Appuyez sur le bouton **probabilités** de la langue pour extraire une liste de langues pour l’entrée utilisateur.

La `HandleLanguageProbabilitiesButtonTap` méthode de la `LanguageRecognizerViewController` classe instancie un `NLLanguageRecognizer` et lui demande de[`Process`](xref:NaturalLanguage.NLLanguageRecognizer.Process*)
texte de l’utilisateur. Il appelle ensuite le module de reconnaissance de langage[`GetNativeLanguageHypotheses`](xref:NaturalLanguage.NLLanguageRecognizer.GetNativeLanguageHypotheses*)
méthode, qui extrait un dictionnaire de langues et les probabilités associées. La `LanguageRecognizerTableViewController` classe restitue ensuite ces langages et probabilités.

```csharp
partial void HandleLanguageProbabilitiesButtonTap(UIButton sender)
{
    UserInput.ResignFirstResponder();
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var recognizer = new NLLanguageRecognizer();
        recognizer.Process(UserInput.Text);
        NSDictionary<NSString, NSNumber> probabilities = recognizer.GetNativeLanguageHypotheses(10);
        PerformSegue(ShowLanguageProbabilitiesSegue, this);
    }
}
```

Les `NLLanguage` valeurs possibles sont les suivantes :

- `Amharic`
- `Arabic`
- `Armenian`
- `Bengali`
- `Bulgarian`
- `Burmese`
- `Catalan`
- `Cherokee`
- `Croatian`
- `Czech`
- `Danish`
- `Dutch`
- `English`
- `Finnish`
- `French`
- `Georgian`
- `German`
- `Greek`
- `Gujarati`
- `Hebrew`
- `Hindi`
- `Hungarian`
- `Icelandic`
- `Indonesian`
- `Italian`
- `Japanese`
- `Kannada`
- `Khmer`
- `Korean`
- `Lao`
- `Malay`
- `Malayalam`
- `Marathi`
- `Mongolian`
- `Norwegian`
- `Oriya`
- `Persian`
- `Polish`
- `Portuguese`
- `Punjabi`
- `Romanian`
- `Russian`
- `SimplifiedChinese`
- `Sinhalese`
- `Slovak`
- `Spanish`
- `Swedish`
- `Tamil`
- `Telugu`
- `Thai`
- `Tibetan`
- `TraditionalChinese`
- `Turkish`
- `Ukrainian`
- `Undetermined`
- `Urdu`
- `Vietnamese`

Une liste complète des langues prises en charge est disponible dans le cadre de la[`NLLanguage`](xref:NaturalLanguage.NLLanguage)
documentation de l’API Enum.

## <a name="tokenizing-text-into-words-sentences-and-paragraphs"></a>Création de jetons de texte dans des mots, des phrases et des paragraphes

L’onglet **jetons** de l’exemple d’application montre comment séparer un bloc de texte en ses mots ou phrases de composant avec [`NLTokenizer`](xref:NaturalLanguage.NLTokenizer)un.

Appuyez sur le bouton **mots** ou **phrases** pour extraire une liste de jetons. Chaque jeton est associé à un mot ou une phrase dans le texte d’origine.

`ShowTokens`divise l’entrée de l’utilisateur en jetons en appelant la[`GetTokens`](xref:NaturalLanguage.NLTokenizer.GetTokens*)
méthode d’un `NLTokenizer`. Cette méthode retourne un tableau de[`NSValue`](xref:Foundation.NSValue)
objets, qui encapsulent chacun une `NSRange` valeur correspondant à un jeton dans le texte d’origine.

```csharp
void ShowTokens(NLTokenUnit unit)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tokenizer = new NLTokenizer(unit);
        tokenizer.String = UserInput.Text;
        var range = new NSRange(0, UserInput.Text.Length);
        NSValue[] tokens = tokenizer.GetTokens(range);
        PerformSegue(ShowTokensSegue, this);
    }
}
```

`LanguageTokenizerTableViewController`génère le rendu d’un jeton unique dans chaque cellule de table. Il extrait un `NSRange` à partir d’un `NSValue`jeton, recherche la chaîne correspondante dans le texte d’origine et définit une étiquette sur la cellule d’affichage de table :

```csharp
public override UITableViewCell GetCell(UITableView tableView, NSIndexPath indexPath)
{
    var cell = TableView.DequeueReusableCell(TokenCell);
    NSRange range = Tokens[indexPath.Row].RangeValue;
    cell.TextLabel.Text = Text.Substring((int)range.Location, (int)range.Length);
    return cell;
}
```

## <a name="tagging-named-entities-and-parts-of-speech"></a>Balisage des entités nommées et des parties de la parole

L’onglet **balises** de l’exemple d’application XamarinNL montre comment utiliser la[`NLTagger`](xref:NaturalLanguage.NLTagger)
classe permettant d’associer des catégories à des jetons d’une chaîne d’entrée.
L’infrastructure de langage naturel intègre une prise en charge intégrée pour la reconnaissance des personnes, des lieux, des organisations et des parties de la parole.

> [!NOTE]
> L’infrastructure de langage naturel prend également en charge les schémas de balisage personnalisés par le biais de modèles ML de base fournis par le développeur. Pour plus d’informations, consultez la session Présentation de l' [infrastructure de langage naturel](https://developer.apple.com/videos/play/wwdc2018/713/) à partir de WWDC 2018.

Appuyez sur le bouton **entités nommées** ou **parties de parole** à extraire :

- Tableau d' `NSValue` objets, chacun encapsulant un `NSRange` pour un jeton dans le texte d’origine.
- Tableau de [`NLTag`](xref:NaturalLanguage.NLTag) valeurs : catégories pour les `NSValue` jetons au même index de tableau.

Dans `LanguageTaggerViewController`, [`NLTagScheme`](xref:NaturalLanguage.NLTagScheme) `NLTagScheme.NameType` et chaque`HandleNamedEntitiesButtonTap` appel ,`ShowTags`en passant un- (pourdespartiesdelaparole)ou(pourlesentités`NLTagScheme.LexicalClass`nommées). `HandlePartsOfSpeechButtonTap`

`ShowTags`crée un `NLTagger`, en l’instanciant avec un `NLTagScheme` tableau de types pour lequel il sera interrogé (dans ce cas, `NLTagScheme` seule la valeur passée). Il utilise ensuite la[`GetTags`](xref:NaturalLanguage.NLTagger.GetTags*)
méthode sur le `NLTagger` pour déterminer les balises pertinentes pour le texte dans l’entrée d’utilisateur.

```csharp
void ShowTags(NLTagScheme tagScheme)
{
    if (!String.IsNullOrWhiteSpace(UserInput.Text))
    {
        var tagger = new NLTagger(new NLTagScheme[] { tagScheme });
        var range = new NSRange(0, UserInput.Text.Length);
        tagger.String = UserInput.Text;

        NLTag[] tags = tagger.GetTags(range, NLTokenUnit.Word, tagScheme, NLTaggerOptions.OmitWhitespace, out NSValue[] ranges);
        NSValue[] tokenRanges = ranges;
        detailViewTitle = tagScheme == NLTagScheme.NameType ? "Named Entities" : "Parts of Speech";

        PerformSegue(ShowEntitiesSegue, this);
    }
}
```

Les balises sont ensuite affichées dans un tableau par `LanguageTaggerTableViewController`le.

Les `NLTag` valeurs possibles sont les suivantes :

- `Adjective`
- `Adverb`
- `Classifier`
- `CloseParenthesis`
- `CloseQuote`
- `Conjunction`
- `Dash`
- `Determiner`
- `Idiom`
- `Interjection`
- `Noun`
- `Number`
- `OpenParenthesis`
- `OpenQuote`
- `OrganizationName`
- `Other`
- `OtherPunctuation`
- `OtherWhitespace`
- `OtherWord`
- `ParagraphBreak`
- `Particle`
- `PersonalName`
- `PlaceName`
- `Preposition`
- `Pronoun`
- `Punctuation`
- `SentenceTerminator`
- `Verb`
- `Whitespace`
- `Word`
- `WordJoiner`

Une liste complète des balises prises en charge est disponible dans le cadre de la[`NLTag`](xref:NaturalLanguage.NLTag)
documentation de l’API Enum.

## <a name="related-links"></a>Liens connexes

- [Exemple d’application – XamarinNL](https://docs.microsoft.com/samples/xamarin/ios-samples/ios12-xamarinnl)
- [Présentation de l’infrastructure en langage naturel](https://developer.apple.com/videos/play/wwdc2018/713/)
- [Langage naturel (Apple)](https://developer.apple.com/documentation/naturallanguage?language=objc)
