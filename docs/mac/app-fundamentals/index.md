---
title: Notions de base de l’application Xamarin. Mac
description: Ce document contient des liens vers des guides qui décrivent les différents concepts nécessaires à la compréhension du développement d’applications Xamarin. Mac.
ms.prod: xamarin
ms.assetid: 5A36B3A7-F197-4AC3-A40D-B2C49362FF06
ms.technology: xamarin-mac
author: conceptdev
ms.author: crdun
ms.date: 12/17/2015
ms.openlocfilehash: 73ec847b697c2d588d0c217bcbf12d4f0b6aa817
ms.sourcegitcommit: 933de144d1fbe7d412e49b743839cae4bfcac439
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 09/04/2019
ms.locfileid: "70291346"
---
# <a name="xamarinmac-application-fundamentals"></a>Notions de base de l’application Xamarin. Mac

## <a name="common-patterns-and-idiomsmacapp-fundamentalspatternsmd"></a>[Modèles courants et idiomes](~/mac/app-fundamentals/patterns.md)

Tout au long des API Apple C#exposées via, certains idiomes et modèles sont reportés de façon répétée. Si vous avez une expérience de programmation avec Xamarin. iOS, ceux-ci peuvent vous paraître familiers. La documentation fait souvent référence à ces modèles et idiomes à plusieurs reprises. ainsi, une bonne compréhension de ces modèles vous aidera à comprendre la documentation que vous trouvez.

## <a name="understanding-mac-apismacapp-fundamentalsmac-apismd"></a>[Fonctionnement des API Mac](~/mac/app-fundamentals/mac-apis.md)

Pour la plupart de votre temps, le développement avec Xamarin. Mac vous permet de penser, de lire C# et d’écrire sans trop de préoccupation avec les API objective-C sous-jacentes. Toutefois, vous aurez parfois besoin de lire la documentation de l’API auprès d’Apple, de traduire une réponse de Stack Overflow à une solution pour votre problème ou de la comparer à un exemple existant.

## <a name="console-appsmacapp-fundamentalsconsolemd"></a>[Applications de console](~/mac/app-fundamentals/console.md)

Vous pouvez également créer des applications console « headless » qui accèdent aux API macOS natives à l’aide de Xamarin. Mac.

## <a name="working-with-xib-filesmacapp-fundamentalsxibmd"></a>[Utilisation des fichiers. XIB](~/mac/app-fundamentals/xib.md)

Cet article traite de l’utilisation des fichiers. XIB créés dans le Interface Builder de Xcode pour créer et gérer des interfaces utilisateur pour une application Xamarin. Mac.

## <a name="storyboardxib-less-user-interface-designmacapp-fundamentalsxibless-uimd"></a>[. Storyboard/. XIB moins conception de l’interface utilisateur](~/mac/app-fundamentals/xibless-ui.md)

Cet article explique comment créer une interface utilisateur de l’application Xamarin. Mac C# directement à partir du code sans utiliser les Interface Builder de Xcode avec les fichiers. Storyboard ou. XIB.

## <a name="working-with-imagesmacapp-fundamentalsimagemd"></a>[Utilisation des images](~/mac/app-fundamentals/image.md)

Cet article traite de l’utilisation des images et des icônes dans une application Xamarin. Mac. Il aborde la création et la gestion des images nécessaires à la création de l’icône de votre C# application et l’utilisation d’images dans le code et le Interface Builder de Xcode.

## <a name="data-binding-and-key-value-codingmacapp-fundamentalsdatabindingmd"></a>[Liaison de données et codage de clé-valeur](~/mac/app-fundamentals/databinding.md)

Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données aux éléments d’interface utilisateur dans les Interface Builder de Xcode. À l’aide de cette technique, vous réduisez C# la quantité de code qui doit être écrite pour votre application Xamarin. Mac. 

## <a name="working-with-databasesmacapp-fundamentalsdatabasesmd"></a>[Utilisation des bases de données](~/mac/app-fundamentals/databases.md)

Cet article aborde l’utilisation du codage clé-valeur et de l’observation clé-valeur pour permettre la liaison de données avec un accès direct aux bases de données SQLite aux éléments d’interface utilisateur dans les Interface Builder de Xcode. Il couvre également l’utilisation de l’ORM SQLite.NET pour fournir l’accès aux données SQLite.

## <a name="working-with-copy-and-pastemacapp-fundamentalscopy-pastemd"></a>[Utilisation du copier-coller](~/mac/app-fundamentals/copy-paste.md)

Cet article traite de l’utilisation de la copie pour fournir des copies et des collages dans une application Xamarin. Mac. Il montre comment utiliser des types de données standard qui peuvent être partagés entre plusieurs applications et comment prendre en charge des données personnalisées dans une application de type « fournir ».

## <a name="sandboxing-a-xamarinmac-appmacapp-fundamentalssandboxingmd"></a>[Bac à sable (sandbox) d’une application Xamarin. Mac](~/mac/app-fundamentals/sandboxing.md)

Cet article traite de la mise en sandbox d’une application Xamarin. Mac pour la publication sur l’App Store. Il couvre tous les éléments qui entrent dans le sandboxing : répertoires de conteneurs, droits, autorisations déterminées par l’utilisateur, séparation des privilèges et application du noyau.

## <a name="playing-sound-with-avaudioplayermacapp-fundamentalssoundsmd"></a>[Émettre du son avec AVAudioPlayer](~/mac/app-fundamentals/sounds.md)

Cet article explique comment utiliser une classe d’assistance pour contrôler la lecture du son à l’aide d’un AVAudioPlayer.

## <a name="reporting-bugsmacapp-fundamentalstroubleshootingmd"></a>[Signalement des bogues](~/mac/app-fundamentals/troubleshooting.md)

Parfois, nous sommes tous bloqués lors de l’utilisation d’un projet, soit sur l’impossibilité d’obtenir une API pour travailler comme je le souhaite, soit pour tenter de contourner un bogue. Notre objectif de Xamarin est de vous permettre de réussir à écrire vos applications mobiles et de bureau, et nous avons fourni des ressources pour vous aider.
