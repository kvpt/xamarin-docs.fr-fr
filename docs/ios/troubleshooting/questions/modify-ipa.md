---
title: Puis-je ajouter des fichiers à ou supprimer des fichiers à partir d’un fichier IPA après l’avoir généré dans Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: lobrien
ms.author: laobri
ms.date: 04/03/2018
ms.openlocfilehash: 047ee06522d4b2c07937e0e1bd9985248a164f01
ms.sourcegitcommit: 7ccc7a9223cd1d3c42cd03ddfc28050a8ea776c2
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/13/2019
ms.locfileid: "67865015"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Puis-je ajouter des fichiers à ou supprimer des fichiers à partir d’un fichier IPA après l’avoir généré dans Visual Studio ?

Oui, il est possible, mais elle nécessitera généralement que vous signer à nouveau le `.app` bundle après avoir apporté la modification.

Notez que la modification de la `.ipa` fichier n’est pas nécessaire dans une utilisation normale. Cet article est fourni uniquement à titre d’information.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemple : suppression d’un fichier à partir d’un `.ipa` archive

Pour cet exemple, supposez que le nom du projet Xamarin.iOS est `iPhoneApp1` et `generated session id` est `cc530d20d6b19da63f6f1c6f67a0a254`

1. Générer le `.ipa` fichier comme d’habitude à partir de Visual Studio.

2. Basculer vers l’hôte de build Mac.

3. Recherchez la build dans le `~/Library/Caches/Xamarin/mtbs/builds` dossier. Vous pouvez coller ce chemin d’accès dans **Finder > Atteindre > accédez au dossier** pour parcourir le dossier dans le Finder. Recherchez le dossier qui correspond au nom de projet. Dans ce dossier, recherchez le dossier qui correspond à la `generated session id` de la build. Il s’agit très probablement le sous-dossier contenant la dernière heure de modification.

4. Ouvrez une nouvelle `Terminal.app` fenêtre.

5. Type `cd` dans la fenêtre Terminal.app et puis glisser -déplacer le `generated session id` dossier vers le `Terminal.app` fenêtre :

    ![](modify-ipa-images/session-id-folder.png "Localiser le dossier d’id de session généré dans le Finder")

6. Tapez la touche Retour pour modifier le répertoire dans le `generated session id` dossier.

7. Décompressez le `.ipa` fichier dans une table temporaire `old/` dossier à l’aide de la commande suivante. Ajuster la `Ad-Hoc` et `iPhoneApp1` nomme en fonction des besoins de votre projet spécifique.

    > ditto -xk bin/iPhone/Ad-Hoc/iPhoneApp1-1.0.ipa old/

8. Conserver la `Terminal.app` fenêtre ouverte.

9. Supprimer les fichiers souhaités à partir de la `.ipa`. Vous pouvez les déplacer vers la Corbeille à l’aide de la recherche, ou supprimez-les sur la ligne de commande à l’aide `Terminal.app`. Pour afficher le contenu de la `Payload/iPhone` dans le Finder, cliquez sur le fichier et sélectionnez **afficher le contenu du Package**.

10. À l’aide de la même approche générale comme à l’étape 3, recherchez le fichier journal sous `~/Library/Logs/Xamarin/MonoTouchVS/` qui a le nom du projet et le `generated session id` dans le nom : ![](modify-ipa-images/build-log.png "Recherchez le journal de génération de projet dans le Finder")

11. Ouvrez le journal de génération de l’étape 10, par exemple en double-cliquant dessus.

12. Recherchez la ligne qui inclut `tool /usr/bin/codesign execution started with arguments: -v --force --sign`.

13. Type `/usr/bin/codesign` dans la fenêtre Terminal.app à l’étape 8.

14. Copiez tous les arguments en commençant par `-v` à la ligne dans l’étape 12, puis collez-les dans la fenêtre Terminal.app.

15. Modifier le dernier argument pour être le `.app` bundle situés au sein de la `old/Payload/` dossier, puis exécutez la commande.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Modifier dans le `old/` répertoire dans un Terminal :

    ```bash
    cd old
    ```

17. Compresser le contenu du répertoire dans un nouveau `.ipa` de fichiers à l’aide de la `zip` commande. Vous pouvez modifier le `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` l’argument de sortie le `.ipa` fichier chaque fois que vous souhaitez :

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Messages d’erreur courants

Si vous voyez `Invalid Signature. A sealed resource is missing or invalid.`, cela signifie généralement que quelque chose a été modifiée dans le `.app` bundle et qui le `.app` bundle n’était pas correctement RE-signé par la suite. Notez également que si vous souhaitez créer un `.ipa` avec un profil de distribution, vous _doit_ générer l’original `.ipa` avec un profil de distribution. Sinon le `Entitlements.xcent` seront incorrectes.

Pour donner un exemple concret de la manière dont cette erreur peut se produire, si vous exécutez la commande suivante `codesign --verify` commande dans la fenêtre de Terminal après l’étape 9, vous verrez l’erreur, ainsi que la cause précise de l’erreur :

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Et le processus de vérification App Store signalera un message d’erreur similaire :

> ERREUR ITMS-90035 : « Signature non valide. Une ressource sealed est manquant ou non valide. Le fichier binaire au chemin d’accès [iPhoneApp1.app/iPhoneApp1] contient une signature non valide. Vérifiez que vous avez connecté votre application avec un certificat de distribution, pas un certificat ad hoc ou un certificat de développement. Vérifiez que les paramètres de signature de code dans Xcode sont corrects au niveau cible (qui remplace les valeurs au niveau du projet). En outre, assurez-vous que le groupe que vous téléchargez a été créé à l’aide d’une cible de la mise en production dans Xcode, pas une cible du simulateur. Si vous êtes certain que vos paramètres de signature de code sont corrects, choisissez « Nettoyer tout » dans Xcode, supprimez le répertoire « build » dans le Finder et regénérer votre cible de la mise en production. Pour plus d’informations, consultez [ https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html ](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html)»
