#Multi-window on AOSP-5.1

##1. Features:

- Add window header and frame for each application.
- Each window can be moved, resized, switched to front/back.
- Each window frame can have shadow.
- When application window's dialog pops up, the application window
  still can be moved, resized, switched to front/back.
- Window header can be automatically shown/hidden, when maximized.

##2. How to do:

###2.0. Understand environments:

- Frameworks:

  Like all another desktops OS, Android have 4 levels: kernel
  level, middle library level, frameworks level, and application
  level. Frameworks is the implemantion of "frameworks level".

- Frameworks/base:

  It is the core of Frameworks. Include start/stop framework
  services, start/stop upper lever applications, manage various
  resources and provide services for upper level applications.

- APP:
  The application own area. It is usually an individual process.
  At least, it has ActiviyThread. and normally, it has one or
  multiple Activity. The related core objects are:

  Activity:

  Each activity can have multiple windows, one is application
  Window, anothers are dialog Window.

  Window:

  Each window has multiple View for display contents, and has an
  ViewRootImple to manage all View on APP side.

  View:

  View is the displaying unit, e.g. button, image, layout ... It
  can be organized as a tree by ViewGroup. The Decor View is the
  tree root, which is known by its' Window and ViewRootImple.

  Surface:

  For real drawing. ViewRootImpl has one Surface for drawing by
  each view. Hardware drawing View (TextureView) has individual
  Surface which will merge with normal Surface in lower-level.

- AMS:

  Activity Manager Service, it is in system service process, and
  manages all APP' Activity. The core related objects:

  ActivityStack:

  Each express an application. It can be focused/unfocused to
  switch current application window to front/back.

  Task:

  Each ActivityStack can have multiple tasks, which is a list
  with the history order. The latest one is used, at present.

  ActivityRecord:

  It is just the Activity in APP, each Task have multiple
  Activity, which is a list with the history order. The latest
  one is shown, at present.

- WMS:

  Window Manager Service, it is in system service process, and
  manages all Window. The core related objects:

  TaskStack:

  Just like ActivityStack in AMS. Its bounds is the reall
  application's displaying area, the bounds can be edited, so
  can let application window move and resize.

  TaskRecord: Just like Task in AMS.

  AppWindowList:

  At least, it can be express the Activity in APP. It includes
  multiple WindowStates, ordered by displaying Z-order. One
  WindowState is for application, another for dialog.

  WindowState:

  Just like Window in APP. It owns a SurfaceControl to manage
  drawing, and communicates with ViewRootImpl in APP through
  Session and IWindow.

  SurfaceControl:

  For manging Surface, e.g. create/destroy, and set attribute.
  The Surface can be transparent or opaque, and also can have
  border padding.

###2.1. Window header/frame:

- mRootContent:

  Decor will load the first View as root View (mRootContent) to
  load the system predefined xml template, and there are 9 kinds
  templates.

- mParentContent:

  Each system predefined templates must provide 'content' layout
  (mParentContent) for loading Application defined xml template.

- Customizing:

  So we can customize the predefined templates to provide Window
  header/frame (e.g. add header/frame layout, adjust 'content'
  layout position).

- Special cases:

  Some Applications use an own View as root. For WanDouJia and
  QQ, we can avoid them to do so, but for WeChat, it won't work,
  so we have to be sure its border always within Window frame.

###2.2. Window operation:

- By changing the bounds in WMS, we can move and resize the
  application Window.

- By focusing/unfocusing the ActivityStack in AMS, we can let
  application Window in front/back.

- Add listener for related View in APP, so can catch all events
  (move, resize, or front/back ...). But in fact, in WMS, we can
  perform the same thing too (we will use it, next).

###2.3. Frame shadow:

- By custom drawing the Decor background and changing predefined
  template, we can add shadow (current implementation). But we
  cann't adjust background color correctly for all applications.

- Use Surface bording padding, we can add shadow, too. It need
  not consider about the applications background color, we will
  implement it in this way, next.

- For hardware drawing View, when Surface has transparent plane,
  it will display half-transparent video. We can mix an opaque
  stream (e.g. background color) to solve this issue temporarily.

###2.4. Popup dialog:

- By limiting the WindowState in WMS, we can limit the dialog
  window size and position. But now, we still limit the dialog
  size in APP side. Next, we will limit it in WMS side.

- By catching events in WMS instead of in APP, we can perform
  all window operation when application window has pop up
  dialog. But our current implementation has bugs to be fix.

###2.5. Show/Hide automatically:

- By View.setVisibility method, we can dynamically show/hide the
  window header layout, when toggle maximize/restore application
  window.

- By catching events in WMS, we can catch the event when we need
  show/hide the window header layout, when maximized.

- By ViewGroup.addView/ViewGroup.removeView, we can let window
  header display in front of the application window's original
  header, when maximized.

###3. Current status:

- Finish window header and frame (100%), also include each special
  cases, e.g.  QQ, WeChat, and WanDouJia.

- Finish window operation (100%), but still can be improved, e.g.
  consider about pop up dialog together.

- Almost finish frame shadow (90%). Current design isn't quite common
  (need guessing some of application's background color). Next, we
  need draw shadow images on surface padding area at WMS.

- Finish part of pop up dialog (65%): Current design is correct, but
  the implementation isn't completed, so some of features can't work.
  We need completely implement dialog with frame operation at WMS.

- Do not support show/hide automatically (0%): We know how to do, and
  next, we shall finish it.



Thanks.

Chen Gang <chengang@emindsoft.com.cn>
