---
title: Débogage d’un plantage natif dans une application Xamarin.Mac
description: Ce document décrit comment déboguer des exceptions qui proviennent du runtime Objective-C. Il traite entre autres des échecs d’assertion, des problèmes de rappels et de la propagation des exceptions.
ms.prod: xamarin
ms.assetid: B0C0CE31-2737-4969-8EA5-D39D3333E9C2
ms.technology: xamarin-mac
author: davidortinau
ms.author: daortin
ms.date: 10/19/2016
ms.openlocfilehash: 40d849ad403f2f47c00be9d3da7b59fc27ce8002
ms.sourcegitcommit: b0ea451e18504e6267b896732dd26df64ddfa843
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 04/13/2020
ms.locfileid: "76725491"
---
# <a name="debugging-a-native-crash-in-a-xamarinmac-app"></a>Débogage d’un plantage natif dans une application Xamarin.Mac

## <a name="overview"></a>Vue d’ensemble

Des erreurs de programmation peuvent parfois provoquer des plantages dans le runtime Objective-C natif. Contrairement aux exceptions de C#, celles-ci ne pointent pas sur une ligne spécifique de votre code que vous pouvez localiser pour la corriger. Elles peuvent parfois être faciles à trouver et à corriger, mais aussi parfois extrêmement difficiles à localiser.

Passons en revue quelques exemples de plantages natifs réels.

## <a name="example-1-assertion-failure"></a>Exemple 1 : Échec d’assertion

Voici les quelques premières lignes d’un plantage dans une application de test simple (ces informations se trouvent dans le panneau **Sortie de l’application**) :

```csharp
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.364 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] *** Assertion failure in -[NSTableView _uncachedRectHeightOfRow:], /SourceCache/AppKit/AppKit-1343.13/TableView.subproj/NSTableView.m:1855
2014-10-15 16:18:02.378 NSOutlineViewHottness[79111:1304993] NSTableView variable rowHeight error: The value must be > 0 for row 0, but the delegate <NSOutlineViewHottness_HotnessViewDelegate: 0xaa01860> gave -1.000.
2014-10-15 16:18:02.381 NSOutlineViewHottness[79111:1304993] (
  0   CoreFoundation                      0x91888343 __raiseError + 195
  1   libobjc.A.dylib                     0x9a5e6a2a objc_exception_throw + 276
  2   CoreFoundation                      0x918881ca +[NSException raise:format:arguments:] + 138
  3   Foundation                          0x950742b1 -[NSAssertionHandler handleFailureInMethod:object:file:lineNumber:description:] + 118
  4   AppKit                              0x975db476 -[NSTableView _uncachedRectHeightOfRow:] + 373
  5   AppKit                              0x975db2f8 -[_NSTableRowHeightStorage _uncachedRectHeightOfRow:] + 143
  6   AppKit                              0x975db206 -[_NSTableRowHeightStorage _cacheRowHeights] + 167
  7   AppKit                              0x975db130 -[_NSTableRowHeightStorage _createRowHeightsArray] + 226
  8   AppKit                              0x975b5851 -[_NSTableRowHeightStorage _ensureRowHeights] + 73
  9   AppKit                              0x975b5790 -[_NSTableRowHeightStorage computeTableHeightForNumberOfRows:] + 89
  10  AppKit                              0x975b4c38 -[NSTableView _totalHeightOfTableView] + 220
```

Les lignes préfixées par des nombres constituent la trace de pile native. Vous pouvez voir ici que le plantage s’est produit quelque part dans `NSTableView`, dans la gestion des hauteurs des lignes. `NSAssertionHandler` déclenche alors une `NSException (objc_exception_throw)`, et nous voyons l’échec d’assertion :

```csharp
rowHeight error: The value must be > 0 for row 0, but the delegate
<NSOutlineView_ViewDelegate: 0xaa01860> gave -1.000
```

À la vue de ceci, il est assez clair qu’une méthode `NSOutlineViewDelegate` retourne un nombre négatif. Voici quel était le problème :

```csharp
public override nfloat GetRowHeight (NSTableView tableView, nint row)
{
    return -1;
}
```

## <a name="example-2-callback-jumped-into-middle-of-nowhere"></a>Exemple 2 : Un rappel aboutit au milieu de nulle part

```csharp
Stacktrace:

 at <unknown> <0xffffffff>
 at (wrapper managed-to-native) MonoMac.AppKit.NSApplication.NSApplicationMain (int,string[]) <IL 0x000a4, 0xffffffff>
 at MonoMac.AppKit.NSApplication.Main (string[]) [0x00041] in /Users/donblas/Programming/xamcore-master/src/AppKit/NSApplication.cs:107
 at NSOutlineViewHottness.MainClass.Main (string[]) [0x00007] in /Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/Main.cs:14
 at (wrapper runtime-invoke) <Module>.runtime_invoke_void_object (object,intptr,intptr,intptr) <IL 0x00050, 0xffffffff>

Native stacktrace:

Debug info from gdb:

(lldb) command source -s 0 '/tmp/mono-gdb-commands.qrHllW'
Executing commands in '/private/tmp/mono-gdb-commands.qrHllW'.
(lldb) process attach --pid 79229
Process 79229 stopped
Executable module set to "/Users/donblas/Programming/Local/NSOutlineViewHottness/NSOutlineViewHottness/bin/Debug/NSOutlineViewHottness.app/Contents/MacOS/NSOutlineViewHottness".
Architecture set to: i386-apple-macosx.
(lldb) thread list
Process 79229 stopped
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 thread #2: tid = 0x142790, 0x9af768d2 libsystem_kernel.dylib`kevent64 + 10, queue = 'com.apple.libdispatch-manager'
 thread #3: tid = 0x142792, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #4: tid = 0x142794, 0x9af6fa6a libsystem_kernel.dylib`semaphore_wait_trap + 10
 thread #5: tid = 0x142795, 0x9af75772 libsystem_kernel.dylib`__recvfrom + 10
 thread #6: tid = 0x142799, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #7: tid = 0x14279a, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #8: tid = 0x14279b, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #9: tid = 0x1427f8, 0x9af75e6e libsystem_kernel.dylib`__workq_kernreturn + 10
 thread #10: tid = 0x1427fe, 0x9af6fa2e libsystem_kernel.dylib`mach_msg_trap + 10
(lldb) thread backtrace all
* thread #1: tid = 0x142776, 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10, queue = 'com.apple.main-thread', stop reason = signal SIGSTOP
 * frame #0: 0x9af75e1a libsystem_kernel.dylib`__wait4 + 10
   frame #1: 0x986bfb25 libsystem_c.dylib`waitpid$UNIX2003 + 48
   frame #2: 0x028ba36d libmono-2.0.dylib`mono_handle_native_sigsegv(signal=11, ctx=0x03115fe0) + 541 at mini-exceptions.c:2323
   frame #3: 0x0290a8bb libmono-2.0.dylib`mono_arch_handle_altstack_exception(sigctx=<unavailable>, fault_addr=<unavailable>, stack_ovf=0) + 155 at exceptions-x86.c:1159
   frame #4: 0x0280b4fd libmono-2.0.dylib`mono_sigsegv_signal_handler(_dummy=<unavailable>, info=<unavailable>, context=<unavailable>) + 445 at mini.c:6861
   frame #5: 0x91ef403b libsystem_platform.dylib`_sigtramp + 43
   frame #6: 0x9a5dd0bd libobjc.A.dylib`objc_msgSend + 45
   frame #7: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #8: 0x9773ba91 AppKit`-[NSApplication sendAction:to:from:] + 548
   frame #9: 0x9773b82d AppKit`-[NSControl sendAction:to:] + 102
   frame #10: 0x97934d36 AppKit`__26-[NSCell _sendActionFrom:]_block_invoke + 176
   frame #11: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #12: 0x97787975 AppKit`-[NSCell _sendActionFrom:] + 161
   frame #13: 0x979188ea AppKit`-[NSButtonCell _sendActionFrom:] + 55
   frame #14: 0x979366e6 AppKit`__48-[NSCell trackMouse:inRect:ofView:untilMouseUp:]_block_invoke965 + 43
   frame #15: 0x96bcec03 libsystem_trace.dylib`_os_activity_initiate + 89
   frame #16: 0x977a3d00 AppKit`-[NSCell trackMouse:inRect:ofView:untilMouseUp:] + 2815
   frame #17: 0x977a2df4 AppKit`-[NSButtonCell trackMouse:inRect:ofView:untilMouseUp:] + 524
   frame #18: 0x977a233b AppKit`-[NSControl mouseDown:] + 762
   frame #19: 0x97cbc112 AppKit`-[_NSThemeWidget mouseDown:] + 378
   frame #20: 0x97d36d74 AppKit`-[NSWindow _reallySendEvent:] + 12353
   frame #21: 0x977201f9 AppKit`-[NSWindow sendEvent:] + 409
   frame #22: 0x976cdc67 AppKit`-[NSApplication sendEvent:] + 4679
   frame #23: 0x9754807c AppKit`-[NSApplication run] + 1003
```

Il s’agit là d’un problème beaucoup plus difficile à localiser. Quand vous voyez `at <unknown> <0xffffffff>` ou `MonoMac.ObjCRuntime.Runtime.GetNSObject (IntPtr ptr)` en haut de la trace de pile managée, cela suggère une tentative d’exécuter du code managé avec un objet qui a été nettoyé en mémoire. La trace de pile native montre `trackMouse:inRect:ofView:untilMouseUp` dans `NSCell _sendActionFrom` : nous gérons donc quelque part un événement de clic, nous tentons un rappel dans C# et nous n’aboutissons nulle part.

En général, ce type d’erreur est difficile à repérer. J’ai ajouté `GC.Collect(2)` à un gestionnaire de bouton pour aider à détecter ce problème (en forçant une garbage collection) pour rendre le problème reproductible. Après avoir scindé l’exemple pendant un certain temps et supprimé des sections de code jusqu’à ce que le problème disparaisse, il s’agissait en fait de [ce bogue](https://bugzilla.xamarin.com/show_bug.cgi?id=23378) :

```csharp
mainWindowController.Window.StandardWindowButton (NSWindowButton.CloseButton).Activated += HandleActivated;
```

Le `NSButton` retourné par `StandardWindowButton()` était nettoyé même si un événement y était inscrit (c’est le bogue). Quand nous essayons d’appeler cet événement en cliquant, si le bouton a été nettoyé, il y a plantage.

Même si ce n’était pas la cause racine de ce problème particulier, des traces de pile comme celle-ci peuvent également être provoquées par des signatures de méthode incorrectes dans les fonctions `[Export]` vers Objective-C. Par exemple, si une méthode attend qu’un paramètre soit `out string` et que vous le tapez en `string`, un plantage peut se produire de la même façon.

## <a name="example-3-callbacks-and-managed-objects"></a>Exemple 3 : Rappels et objets gérés

De nombreuses API Cocoa impliquent d’être « rappelées » par la bibliothèque quand un événement se produit, ce qui vous donne l’opportunité de répondre, ou quand des données sont nécessaires pour effectuer une tâche. Même si vous pensez principalement aux modèles **Delegate** et **DataSource**, il existe une multitude d’API qui fonctionnent de cette manière. Par exemple, quand vous remplacez les méthodes d’un `NSView`, puis que vous l’insérez dans l’arborescence d’éléments visuels, vous vous attendez à ce que AppKit vous rappelle quand certains événements se produisent.

Dans presque tous les cas, Xamarin.Mac empêche correctement le nettoyage de l’objet managé cible de ces rappels alors même qu’il peut encore être rappelé. Cependant, de rares bogues dans la liaison peuvent perturber ceci. Quand cela se produit, vous pouvez subir des plantages désagréables similaires à ceci :

```csharp
Thread 0 Crashed:: Dispatch queue: com.apple.main-thread

0   libsystem_kernel.dylib              0x98c2f69a __pthread_kill + 10
1   libsystem_pthread.dylib             0x90341f19 pthread_kill + 101
2   libsystem_c.dylib                   0x9453feee abort + 156
3   libmonosgen-2.0.dylib               0x020bfba5 mono_handle_native_sigsegv + 757
4   libmonosgen-2.0.dylib               0x0210b812 mono_arch_handle_altstack_exception + 162
5   libmonosgen-2.0.dylib               0x0200c55e mono_sigsegv_signal_handler + 446
6   libsystem_platform.dylib            0x9513003b _sigtramp + 43
7   ???                                 0xffffffff 0 + 4294967295
8   libmonosgen-2.0.dylib               0x0200c3a0 mono_sigill_signal_handler + 48
9   com.apple.AppKit                    0x99f76041 -[NSView setFrame:] + 448
10  com.apple.AppKit                    0x9a1fd4ea -[NSToolbarView adjustToWindow:attachedToEdge:] + 198
11  com.apple.AppKit                    0x9a1fd414 -[NSToolbar _adjustViewToWindow] + 68
12  com.apple.AppKit                    0x9a01eb0d -[NSToolbar _windowWillShowToolbar] + 79

```

Ce guide vous aide à détecter les bogues de cette nature s’ils se produisent, à les signaler correctement pour qu’ils puissent être résolus et à les contourner dans votre code en attendant.

### <a name="locating"></a>Localisation

Dans presque tous les cas avec des bogues de cette nature, le symptôme principal est que des plantages natifs se produisent avec quelque chose qui ressemble à `mono_sigsegv_signal_handler` ou à `_sigtrap` dans les trames du haut de la pile. Cocoa tente d’effectuer un rappel dans votre code C#, tente d’atteindre un objet qui a été nettoyé et plante. Tous les plantages avec ces symboles ne sont cependant pas provoqués par un problème de liaison comme celui-ci : vous devez donc effectuer certaines recherches supplémentaires pour vérifier qu’il s’agit bien de ce problème.

Ces bogues sont difficiles à repérer parce qu’ils se produisent seulement **après** qu’un garbage collection a supprimé l’objet en question. Si vous pensez avoir rencontré un de ces bogues, ajoutez le code suivant quelque part dans votre séquence de démarrage :

```csharp
new System.Threading.Thread (() =>
{
    while (true) {
         System.Threading.Thread.Sleep (1000);
         GC.Collect ();
    }
}).Start ();
```

Ceci force votre application à exécuter le Garbage Collector toutes les secondes. Réexécutez votre application et essayez de reproduire le bogue. Si elle plante tout de suite, ou de façon cohérente et non aléatoire, vous êtes sur la bonne voie.

### <a name="reporting"></a>Signalement

L’étape suivante consiste à signaler le problème à Xamarin, pour que la liaison puisse être corrigée dans les versions futures. Si vous êtes détenteur d’une licence professionnelle ou d’entreprise, ouvrez un ticket sur

[visualstudio.microsoft.com/vs/support/](https://visualstudio.microsoft.com/vs/support/)

Sinon, recherchez un problème existant :

- Consultez les [forums Xamarin.Mac](https://forums.xamarin.com/categories/xamarin-mac)
- Recherchez dans le [dépôt des problèmes](https://github.com/xamarin/xamarin-macios/issues)
- Avant de devenir des problèmes GitHub, les problèmes Xamarin étaient suivis sur [Bugzilla](https://bugzilla.xamarin.com/describecomponents.cgi). Recherchez-y des problèmes correspondants.
- Si vous ne trouvez pas de problème correspondant, ouvrez un nouveau problème dans le [dépôt de problèmes GitHub](https://github.com/xamarin/xamarin-macios/issues/new).

Les problèmes GitHub sont tous publics. Il n’est pas possible de masquer des commentaires ou des pièces jointes.

Dans la mesure du possible, essayez d’inclure :

- Un exemple simple reproduisant le problème. Si vous pouvez en fournir un, c’est **inestimable**.
- La trace de pile complète du plantage.
- Le code C# se rapportant au plantage.   

### <a name="working-around"></a>Contournement

Une fois que vous avez identifié le problème, le résoudre avec une solution de contournement jusqu’à ce que la liaison soit corrigée peut être simple. L’objectif est d’empêcher que l’objet (**View**, **Delegate**, **DataSource**) qui est incorrectement supprimé quitte la mémoire en conservant une référence ouverte.

Pour les cas simples où il n’existe qu’une seule instance de l’objet, changez ce code :

```csharp
void AddObject ()
{
    item.View = new MyView ();
    ...
}
```

Et utilisez une variable statique comme celle-ci :

```csharp
static NSObject view;
...

void AddObject ()
{
    view = new MyView ();
    item.View = view;
    ...
}
```

Dans les cas où plusieurs instances peuvent être créées, vous pouvez utiliser un `HashSet` statique :

```csharp
static HashSet<NSObject> collection = new HashSet<NSObject> ();
...

void AddObject ()
{
    item.View = new MyView ();
    collection.Add (item.View );
    ...
}
```

Une fois que la liaison a été résolue et que vous avez effectué une mise à niveau avec la version de Xamarin.Mac incluant le correctif, le code de contournement peut être supprimé.

## <a name="exception-bubbling-and-objective-c"></a>Propagation des exceptions et Objective-C

N’autorisez jamais une exception C# à « laisser échapper » du code managé vers la méthode Objective-C appelante. Si vous le faites, les résultats ne sont pas définis, mais ils impliquent généralement un plantage. D’une façon générale, nous faisons tout ce qui est possible pour faire apparaître des informations utiles sur les plantages natifs et managés afin de vous aider à résoudre ces problèmes rapidement.

Sans trop entrer dans les raisons techniques, la configuration de l’infrastructure pour intercepter les exceptions managées à chaque limite managée/native est coûteuse en ressources, et il existe par ailleurs _beaucoup_ de transitions qui se produisent dans de nombreuses opérations courantes. De nombreuses opérations, en particulier celles qui impliquent le thread d’interface utilisateur, doivent se terminer rapidement, sans quoi votre application risque de fonctionner par à-coup et avoir des performances inacceptables. Beaucoup de ces rappels font des choses très simples qui ont rarement la possibilité de lever des exceptions : ce surcroît de charge serait à la fois coûteux et inutile dans ces cas.

Ainsi, nous n’allons pas configurer ces instructions try/catch pour vous. Là où votre code fait des choses non triviales (disons, plus que retourner un booléen ou un calcul simple), vous pouvez essayer d’intercepter les exceptions vous-même.
