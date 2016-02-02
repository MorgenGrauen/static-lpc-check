Dieses Script prueft Regionscode auf eine Reihe von Fehlern bzw. zeigt
Funktionen und Properties an, denen man bei der Abnahme besonderes Augenmerk
schenken sollte.

Im dem Script 'check' muss vor Benutzung  die Variable MUDLIB auf das
Verzeichnis der Mudlib gesetzt werden.
Anschliessend erfolg die Benutzung durch Aufruf von 'check':
  ./check <region> <logfile>

Die Ausgabe des Scriptes erfolgt primaer in Logdateien, die in logs/ abgelegt
werden. Hierbei werden momentan 4 Stufen unterschieden:
* Aenderung noetig (<logfile>.change_required)
* Bitte pruefen (<logfile>.please_check)
* Formatierung (<logfile>.formatting)
* Kosmetik (<logfile>.cosmetics)

Die Wichtigkeit und Relevanz dieser Punkte muss der RM in letzter Konsequenz
ggf. selber entscheiden. Hierzu sollten der allgemeine Regionsleitfaden, die
jeweiligen Regionsanforderungen und ggf. die Manpages 'goodstyle' und
'effizienz' beachtet werden.

Aenderungen noetig:
------------------
* Pfade in /players/
  Pfade in /players/ duerfen von angeschlossenen Gebieten nicht benutzt
  werden, da der betreffene RM den Code selber nicht warten kann.
* Lambdas
  Lambdas werden in der Regel nicht mehr angeschlossen, weil sie schwer
  lesbar sind. Alternativen sind z.B. inlines closures.
* Objekte ohne strong_types oder strict_types
  Die striktere Typpruefung hilft dem Programmierer bereits bei der Ent-
  wicklung und vor Anschluss eine Menge Fehler zu finden. Daher sollte eins
  von beiden Pragmas in neuem Code immer benutzt werden.
* veraltete Efuns, Lfuns und Properties
  Es gibt eine ganze Reihe von alten Funktionen und Properties, die aus dem
  Driver oder der Mudlib entfernt wurden oder in absehbarer Zeit entfernt
  werden. Diese duerfen in neuem Code keinesfalls verwendet werden.
* unerwuenschte Lfuns, Efuns, Properties und Events
  Es gibt einige Properties und Events, die in normalen Regionscode nicht
  verwendet werden sollten, z.B. weil sie ausschliesslich durch die Mudlib
  benutzt werden (Eventnamen) oder weil ihre interne Datenstruktur komplex ist
  und die Mudlib ein Interface dafuer bereitstellt. Ein weiteres Beispiel ist
  die Verwendung von do_damage(), in aller Regel sollte stattdessen Defend()
  genutzt werden.
* alte Hooks
  Die alten Hooks, welche ueber eine Prop gesetzt werden, wurden durch ein
  neues Hooksystem abgeloest, bei dem es mehr als nur ein Objekt pro Hook
  geben kann. s. Manpage std/hooks.
* Umlaute
  Umlaute haben im Mud bisher nix zu suchen. ;-)
* Clonen von /std/thing
  Mit Ausnahme von /std/corpse sollte aus /std/ nichts geclont und manuell
  konfiguriert werden, da die Zuordnung von Objekten wie /std/thing in
  Spielerinventaren schwierig ist und Typos/Bugs/Ideen nicht beim richtigen
  Magier ankommen.
* ueberschriebenes move()
  Beim Ueberschreiben von move() kommt es haeufig zu Fehlern. Heutzutage
  sollten stattdessen PreventMove() und NotifyMove() ueberschrieben werden.
* PreventMove()/NotifyMove() ohne die geerbten Funktionen aufzurufen
  Diese beiden Funktionen _muessen zwingend_ ihre geerbten Varianten per
  :: aufrufen!
* call_other(x, "???")
  Zum Laden von Objekten soll load_object() benutzt werden.
* Obsolete Variante von Inline Closures: (: :)
  Diese Syntax dient im Driver nur der MudOS-Kompatibilitaet. Bitte nicht
  in neuen Gebieten benutzen.
* process_string(): @@*@@
  process_string() ist ein altes Relikt und kann Sicherheitsprobleme
  verursachen. Dank Querymethoden sollte die Verwendung unnoetig sein.

Bitte pruefen (please check)
----------------------------
* Header (*.h) mit #pragmas...
  Pragmas in Headern wirken sich auf alle inkludierenden Objekte aus. Eine
  Reihe von Headern kann auch woanders verwendet werden. Es muss genau
  geprueft werden, ob die betreffenden Header u.U. woanders genutzt werden
  (koennten). Wenn ja, sollte man dort keine Pragmas setzen um nicht anderer
  Magier Pragmas zu ueberschreiben.
* Efuns, Lfuns, Properties etc. deren Verwendung der RM pruefen sollte
  Manche Funktionen werden gerne so benutzt, dass es buggt. Daher sollt der RM
  bei diesen besonders gruendlich schauen, ob sie korrekt verwendet werden.
  Z.B. clone_object(). Nach dieser Funktion muss sichergestellt werden, dass
  das geclonte Objekte entweder erfolgreich bewegt oder wieder zerstoert wird.
* Verwendung von 'static'
  static kann weitgehend als veraltet gelten. Im Falle von Variablen sollte
  man das eingaengigere 'nosave' benutzen, bei Funktionen 'protected'.
  Eine der wenigen Ausnahmen sind momentan hart-kodierte Query- und
  Setmethoden _set_propstring() und _query_propstring(), die muessen momentan
  noch 'static' sein. (Bitte hierzu auch die Manpage 'modifier' beachten!)

Formatierung (formatting)
-------------------------
* Tabulatoren
  Das Einruecken per Tabulatoren sollte man eher lassen. Falls jemand den Code
  mal im Mud lesen muss, wird bei einem Tab immer um 8 Zeichen eingerueckt und
  im Ergebnis ist so eingerueckter Code praktisch nicht mehr zu lesen, weil
  die Zeilen viel zu lang werden.
* Zeilen laenger als 78.
  Siehe Tabulatoren, manchmal muss man Code einfach auch im Mud lesen koennen.
  Ausserdem sind ueberlange Zeilen fuer die Augen eh anstrengend.

Kosmetik (cosmetics)
--------------------
* Kleingeschriebenes Du/Dir/Dich.
  Auch wenn es nach neuer dt. Rechtschreibung nicht mehr grossgeschrieben
  werden muss, wird es im Mud meistens noch so gemacht. Hier sollte der RM auf
  Konsistenz in seinen Gebieten achten.



Anhang
======
Bei einigen unerwuenschten Dingen soll kurz erlaeutert werden, warum sie das
sind.

Lfuns:
* command_me(): 
  In aller Regel sollte die Efun command() benutzt werden, speziell wenn das
  Lebewesen selber ein Kommando ausfuehrt. command_me() ist nur notwendig,
  wenn ein anderes Lebewesen 'static' Kommandos ausfuehren soll.
* _query_ac() / _set_ac(): 
  Die Benutzung dieser Funktionen kann zu _extremen_ Inkonsistenzen und
  beliebig hohen P_AC bei Reparaturen fuehren. Extreme Vorsicht bitte!
* _query_item_damaged() / _set_item_damaged():
  S.o. Soll ein Item nicht beschaedigt werden koennen, ist eine geeignete
  Funktion Damage() zu benutzen!
* _item_damaged():
  Zur Kontrolle, ob ein Item beschaedigt werden kann, sollte Damage() benutzt
  werden.
* SpellDefend():
  Das Abhaengen von Gildenfaehigkeiten sollte nur in Ausnahmefaellen erfolgen
  und nicht durch die Bank. RMs sollten darauf achten.
* NoParaObjects():
  Diese Lfun ist obsolet. Bitte P_PARA nutzen.
  
Props:
* P_EXTRA_LOOK
  Ist veraltet. S. AddExtraLook(), RemoveExtraLook()
* P_RESISTANCE
  Ist veraltet. S. P_RESISTANCE_STRENGTHS
* P_DAMAGED
  Die direkte Manipulation dieser Prop sollte nicht stattfinden! Beschaedigen
  oder Reparieren darf ausschliesslich via Damage() erfolgen.
* P_NOMAGIC
  Magier abzuhaengen bzw. zu erschweren sollte nicht wahllos erfolgen. Die
  Benutzung dieser Prop sollten RMs also speziell im Auge behalten.
* P_TMP_*
  Die verschiedenen Hooks, die ueber das Setzen der P_TMP_* Properties
  aktiviert werden, sollten kontrolliert verwendet. Einerseits, weil sie sehr
  maechtig sind (die Benutzung muss oft die Balance genehmigen), andererseits
  weil man hierbei leicht Fehler machen kann und zu guter Letzt, weil immer
  nur ein Objekt einen solchen Hook belegen darf. Daher ist darauf zu achten,
  dass die Hooks nicht laenger als noetig belegt werden und dass man nicht
  anderer Objekte Hooks ueberschreibt!
* P_RESISTANCE_STRENGTHS
  Resistenzen in fremden Objekten sollte nie ueber die Manipulation dieser
  Prop veraendert werden. Stattdessen immer AddResistanceModifier() benutzen!
* P_PREVENT_PILE
  Das Verhindern der Kramhaufen, die aus Leichen entstehen, sollte nur im
  Ausnahmefall in Lagerraeumen geschehen, aus denen z.B. Spieler ihren Kram
  "herausangeln" koennen.
* P_SKILL_ATTRIBUTES
  Die Datenstruktur dieser Prop ist komplex. Bitte auf gar keinen Fall ueber
  die Prop direkt zugreifen, sondern immer die Funktionen der Mudlib dafuer
  benutzen!

Events:
* Eventnamen, die mit evt_lib_ oder evt_guild_ beginnen
  Diese Events duerfen ausschliessliche durch die Lib oder Gilden verwendet
  werden!

Efuns:
* clone_object()
  Nach dieser Funktion muss geprueft werden, ob das Objekt in ein anderes
  Objekt bewegt wurde. Ansonsten ist es zu zerstoeren.
* write_file()
  Diese Funktion wird desoefteren zum Loggen verwendet. Hierzu ist die in
  aller Regel denkbar ungeeignet: Zum einen werden Logfiles hiermit staendig
  groesser und muellen die Platte voll und zum anderen liegen sie damit
  meistens nicht in /log/ (wo sie hingehoeren!). Bitte log_file() verwenden!
* shout()
  Diese Funktion bruellt ggf. durch das ganze Mud. RMs sollten genau pruefen,
  ob das notwendig ist oder das Gebruelle auf bestimmte Regionen/Gebiete
  beschraenkt werden kann.
* enable_commands() / disable_commands()
  Diese Efuns sind nur in seltenen Ausnahmefaellen noetig und sinnvoll.
  Speziell in NPC sind sie _nicht_ noetig.

Dank an
=======
Arathorn, Chagall, Ennox, Vanion fuer das Beisteuern von zu pruefenden
Punkten.
Besonderen Dank an Chagall fuer das Ueberarbeiten der ersten
Quick&Dirty-Version des Scripts.
(hab ich wen vergessen? -> bitte melden)


Ich hoffe, dass euch dieses Tool bei der Abnahme von Gebieten das Leben etwas
erleichtert. Wenn euch etwas unklar ist, sprecht mich (Zesstra) bitte einfach an.

