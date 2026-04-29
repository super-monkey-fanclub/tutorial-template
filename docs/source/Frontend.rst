
Frontend
=============

Overview
=============

The front end of this application is different each device. This doc will cover some of the front end choices and inner workings, but in terms of what you can expect to see throughout the app, you can find:

Fonts
-----

Android: Roboto

iOS/macOS: San Francisco

Windows: Segoe UI

Linux/web: platform/browser default sans-serif chosen by Flutter


Colours
-------

One consistent colour theme, featuring the colours represented in the University of Portsmouth logo.

Seed colour: 0xFF003087

Primary colour: 0xFF003087

Secondary colour: 0xFF7B2D8E

Background colour: 0x29FFFFFF 

Error colour: 0xFFBA1A1A

Text colour: Not fixed, readable contrast via code:

.. code-block:: dart

  style: Theme.of(context).textTheme.headlineSmall?.copyWith(
    fontWeight: FontWeight.w700,

