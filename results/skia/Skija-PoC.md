# Federico's notes

## Some questions
- How do I effectively draw the widgets to a `GC` if I can't create one?

SOLVED: extend from `Scrollable` and do `new GC(this)`

## Constraints
- `Button` needs to ``extend Scrollable`` in order to avoid implementing methods like `callWindowProc`, `windowProc` and `windowClass` (`Scrollable` implements them all).

## Issues
- Hook methods (OS-dependent) are essential to the event-triggering _e.g._ even `CCombo` relies on the hook method `Button::wmCommandChild` (on Windows) which means that providing an OS-agnostic version of `Button` could simply break `CCombo`

How to deal with this issue? I need to find a way to hook into the event-triggering mechanism that is OS-agnostic and that will end up sending the event to the display queue _i.e._ it will reach `Display::postEvent` **without** passing through `Button.wmCommandChild`:

```java
Display.postEvent(Event) line: 3673	
Button(Widget).sendEvent(int, Event, boolean) line: 1200	
Button(Widget).sendSelectionEvent(int, Event, boolean) line: 1215	
Button(Widget).sendSelectionEvent(int) line: 1206	
Button.wmCommandChild(long, long) line: 1303	// I need to bridge this call somehow
CCombo(Control).WM_COMMAND(long, long) line: 4878	
CCombo(Control).windowProc(long, int, long, long) line: 4730	
...
```