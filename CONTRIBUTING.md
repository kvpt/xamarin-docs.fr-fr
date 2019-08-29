---
ms.openlocfilehash: 14d4f5de500c8e8ced6cbeb67019f9152ed63df3
ms.sourcegitcommit: 1dd7d09b60fcb1bf15ba54831ed3dd46aa5240cb
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118912"
---
# <a name="contributing"></a>Contribuer

Nous vous remercions de l’intérêt que vous portez à la documentation Xamarin à travers vos contributions.

Cette page décrit le processus simple de mise à jour de contenu dans la [documentation Xamarin](https://docs.microsoft.com/xamarin).

- [Contrat de licence de contribution (CLA)](LICENSE)

## <a name="process-for-contributing"></a>Processus de contribution

### <a name="small-changes--edits"></a>Modifications et changements mineurs

Pour apporter des corrections et des mises à jour mineures, vous pouvez cliquer sur le bouton **Modifier** de n’importe quelle page et utiliser le site web GitHub pour contribuer, ou effectuer les étapes suivantes :

1. Dupliquez le dépôt `MicrosoftDocs/xamarin-docs`.

2. Créez une `branch` pour vos modifications.

3. Écrivez votre contenu. Reportez-vous au [modèle](contributing-guidelines/template.md) et au [guide de style](contributing-guidelines/voice-tone.md).

4. Soumettez une demande de tirage (pull request) de votre branche à `MicrosoftDocs/xamarin-docs/live`.

5. Effectuez les mises à jour nécessaires de votre branche comme convenu avec l’équipe via la demande de tirage.

6. Les personnes chargées de la maintenance fusionneront votre demande de tirage une fois que les commentaires auront été appliqués et que votre modification sera correcte. Elle apparaîtra sur docs.microsoft.com peu après.


> [!NOTE]
> Si votre demande de tirage répond à un problème existant, ajoutez le mot clé `Fixes #Issue_Number` au message de validation ou à la description de la demande de tirage. Le problème pourra ainsi être clôturé automatiquement dès que la demande de tirage aura été fusionnée. Pour plus d’informations, consultez [Closing issues via commit messages](https://help.github.com/articles/closing-issues-via-commit-messages/).


### <a name="big-changes-or-new-content"></a>Changements importants ou nouveau contenu

Pour des contributions importantes et un nouveau contenu, [ouvrez un problème](https://github.com/MicrosoftDocs/xamarin-docs/issues) décrivant l’article que vous voulez rédiger et son rapport avec le contenu existant. Le contenu situé dans le dossier docs est organisé en sections, qui sont elles-mêmes organisées par zone produit (par exemple, Android et iOS). Essayez de déterminer le dossier qui correspond le mieux à votre nouveau contenu. 

**Obtenez des commentaires sur votre proposition via le problème avant de commencer à écrire.**

S’il s’agit d’une nouvelle rubrique, vous pouvez utiliser le [fichier de modèle](../contributing-guidelines/template.md) comme point de départ. Il contient les recommandations rédactionnelles et explique aussi les métadonnées nécessaires pour chaque article, comme les informations sur l’auteur.

Ajoutez les images et autres ressources statiques au sous-dossier appelé **\<mypage>-images**. Si vous créez un dossier pour le contenu, ajoutez un dossier images au nouveau dossier.

#### <a name="example-structure"></a>Exemple de structure

```
docs
    /android
        mypage.md
        /mypage-images
            some-image.png
```

Veillez à respecter la syntaxe Markdown appropriée. Pour plus d’informations, consultez le [guide de style](../contributing-guidelines/template.md).

Les étapes de la soumission sont en fait les mêmes que pour les modifications mineures ([ci-dessus](#process-for-contributing)).

L’équipe Xamarin examine votre demande de tirage et vous fait savoir (via des commentaires sur la demande de tirage) si la modification est recevable en l’état ou si d’autres mises à jour/modifications sont nécessaires à son approbation.

Les personnes chargées de la maintenance fusionneront alors votre demande de tirage une fois que les commentaires auront été appliqués et que votre modification sera correcte.

Nous envoyons (push) toutes les validations de la branche principale au site en ligne à une certaine fréquence. Vous pourrez voir vos contributions sur https://docs.microsoft.com/xamarin/.

## <a name="dos-and-donts"></a>À faire et à ne pas faire

Voici une courte liste de règles directrices que vous devez garder à l’esprit quand vous contribuez à la documentation .NET.

- **À ne pas faire** : Nous surprendre avec des demandes de tirage démesurées. Soumettez plutôt un problème et démarrez une discussion pour convenir avec nous de la direction à prendre avant d’investir beaucoup de votre temps.
- **À faire** : Lire le [guide de style](contributing-guidelines/template.md) et les recommandations sur le [style et le ton](contributing-guidelines/voice-tone.md).
- **À faire** : Utiliser le fichier de [modèle](contributing-guidelines/template.md) comme point de départ de votre travail.
- **À faire** : Créer une branche distincte dans votre duplication (fork) avant de travailler sur les articles.
- **À faire** : Suivre le [workflow GitHub Flow](https://guides.github.com/introduction/flow/).
- **À faire** : Bloguer et tweeter (ou autre) régulièrement à propos de vos contributions.

> [!NOTE]
> Vous remarquerez peut-être que certaines rubriques ne respectent pas toutes les recommandations spécifiées ici et dans le [guide de style](contributing-guidelines/template.md). Nous travaillons actuellement à une cohérence globale du site. 


