---
title: Puis-je ajouter ou supprimer des fichiers dans un fichier de vue de la loi après l’avoir généré dans Visual Studio ?
ms.topic: troubleshooting
ms.prod: xamarin
ms.assetid: 6C3082FB-C3F1-4661-BE45-64570E56DE7C
ms.technology: xamarin-ios
author: davidortinau
ms.author: daortin
ms.date: 04/03/2018
ms.openlocfilehash: e3b535b8624f03e9c832f1719237409cb9ff26c2
ms.sourcegitcommit: 008bcbd37b6c96a7be2baf0633d066931d41f61a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/22/2020
ms.locfileid: "86939904"
---
# <a name="can-i-add-files-to-or-remove-files-from-an-ipa-file-after-building-it-in-visual-studio"></a>Puis-je ajouter ou supprimer des fichiers dans un fichier de vue de la loi après l’avoir généré dans Visual Studio ?

Oui, c’est possible, mais il est généralement nécessaire de signer à nouveau le `.app` Bundle après avoir apporté la modification.

Notez que la modification du `.ipa` fichier n’est pas nécessaire dans le cas d’une utilisation normale. Cet article est fourni exclusivement à titre d’information.

## <a name="example-removing-a-file-from-a-ipa-archive"></a>Exemple : suppression d’un fichier d’une `.ipa` Archive

Pour cet exemple, supposons que le nom du projet Xamarin. iOS est `iPhoneApp1` et que `generated session id` est`cc530d20d6b19da63f6f1c6f67a0a254`

1. Générez le `.ipa` fichier normalement à partir de Visual Studio.

2. Basculez vers l’hôte de build Mac.

3. Recherchez la build dans le `~/Library/Caches/Xamarin/mtbs/builds` dossier. Vous pouvez coller ce chemin dans **finder > go > accéder** au dossier pour parcourir le dossier dans Finder. Recherchez le dossier qui correspond au nom du projet. Dans ce dossier, recherchez le dossier qui correspond au `generated session id` de la Build. Il s’agit probablement du sous-dossier qui a l’heure de modification la plus récente.

4. Ouvrez une nouvelle `Terminal.app` fenêtre.

5. Tapez `cd` dans la fenêtre Terminal. app, puis faites glisser & déposez le `generated session id` dossier dans la `Terminal.app` fenêtre :

    ![Recherche du dossier d’ID de session généré dans le Finder](modify-ipa-images/session-id-folder.png)

6. Tapez la clé de retour pour modifier le répertoire dans le `generated session id` dossier.

7. Décompressez le `.ipa` fichier dans un `old/` dossier temporaire à l’aide de la commande suivante. Ajustez `Ad-Hoc` les `iPhoneApp1` noms et en fonction des besoins de votre projet particulier.

    > Ditto-XK bin/iPhone/Ad-Hoc/iPhoneApp1-1.0. Loi Old/

8. Laissez la `Terminal.app` fenêtre ouverte.

9. Supprimez les fichiers souhaités du `.ipa` . Vous pouvez les déplacer vers la corbeille à l’aide de Finder ou les supprimer sur la ligne de commande à l’aide de `Terminal.app` . Pour afficher le contenu du `Payload/iPhone` fichier dans Finder, cliquez sur le fichier, puis sélectionnez **afficher le contenu du package**.

10. À l’aide de la même approche générale que à l’étape 3, recherchez le fichier journal sous `~/Library/Logs/Xamarin/MonoTouchVS/` qui contient à la fois le nom du projet et le `generated session id` dans le nom : ![ localiser le journal de génération du projet dans le Finder](modify-ipa-images/build-log.png)

11. Ouvrez le journal de génération créé à l’étape 10, par exemple en double-cliquant dessus.

12. Recherchez la ligne qui contient `tool /usr/bin/codesign execution started with arguments: -v --force --sign` .

13. Tapez `/usr/bin/codesign` dans la fenêtre Terminal. app de l’étape 8.

14. Copiez tous les arguments à `-v` partir de la ligne de l’étape 12, puis collez-les dans la fenêtre Terminal. app.

15. Modifiez le dernier argument pour qu’il soit le `.app` Bundle situé dans le `old/Payload/` dossier, puis exécutez la commande.

    ```bash
    /usr/bin/codesign -v --force --sign SOME_LONG_STRING in/iPhone/Ad-Hoc/iPhoneApp1.app/ResourceRules.plist --entitlements obj/iPhone/Ad-Hoc/Entitlements.xcent old/Payload/iPhoneApp1.app
    ```

16. Accédez au `old/` répertoire dans le terminal :

    ```bash
    cd old
    ```

17. Compressez le contenu du répertoire dans un nouveau `.ipa` fichier à l’aide de la `zip` commande. Vous pouvez modifier l' `"$HOME/Desktop/iPhoneApp1-1.0.ipa"` argument pour générer le `.ipa` fichier où vous le souhaitez :

    ```bash
    zip -yr "$HOME/Desktop/iPhoneApp1-1.0.ipa" *
    ```

## <a name="common-error-messages"></a>Messages d’erreur courants

Si vous voyez `Invalid Signature. A sealed resource is missing or invalid.` , cela signifie généralement que des éléments ont été modifiés au sein du `.app` Bundle et que l' `.app` offre groupée n’a pas été correctement signée à nouveau par la suite. Notez également que si vous souhaitez créer un `.ipa` avec un profil de distribution, vous _devez_ créer l’original `.ipa` avec un profil de distribution. Dans le cas contraire, le `Entitlements.xcent` sera incorrect.

Pour donner un exemple concret de la façon dont cette erreur peut se produire, si vous exécutez la `codesign --verify` commande suivante dans la fenêtre de terminal après l’étape 9, vous verrez l’erreur avec la cause exacte de l’erreur :

```bash
$ codesign -dvvv --no-strict --verify old/Payload/iPhoneApp1.app
old/Payload/iPhoneApp1.app: a sealed resource is missing or invalid
file missing: /Users/macuser/Library/Caches/Xamarin/mtbs/builds/iPhoneApp1/cc530d20d6b19da63f6f1c6f67a0a254/old/Payload/iPhoneApp1.app/MyFile.png
```

Et le processus de vérification de l’App Store signalera un message d’erreur similaire :

> ERREUR ITMS-90035 : «signature non valide. Une ressource sealed est manquante ou non valide. Le fichier binaire dans le chemin d’accès [iPhoneApp1. app/iPhoneApp1] contient une signature non valide. Assurez-vous d’avoir signé votre application avec un certificat de distribution, et non un certificat ad hoc ou un certificat de développement. Vérifiez que les paramètres de signature de code dans Xcode sont corrects au niveau cible (qui remplace toutes les valeurs au niveau du projet). En outre, assurez-vous que le bundle que vous chargez a été créé à l’aide d’une cible de mise en version dans Xcode, et non d’une cible de simulateur. Si vous êtes certain que vos paramètres de signature de code sont corrects, choisissez « nettoyer tout » dans Xcode, supprimez le répertoire « Build » dans le Finder, puis régénérez votre cible de mise en version. Pour plus d’informations, consultez [https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html](https://developer.apple.com/library/ios/documentation/Security/Conceptual/CodeSigningGuide/Introduction/Introduction.html) «
