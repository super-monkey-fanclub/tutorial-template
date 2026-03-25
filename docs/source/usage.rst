Usage
=====

.. _startdatabase:

Start Database
------------

To start the database (as it is local during development), cd to backend and run the command ''python manage.py runserver''

.. code-block:: console

   cd backend
   ..\.venv\Scripts\activate
   python manage.py runserver 0.0.0.0:8000

Start App
----------------

To run the app, navigate to ''main.dart'' and run without debugging. Choose any browser when it is prompted.
Alternatively, run this in a new terminal (after the server is running)

.. code-block:: console

   cd "unify_frontend"
   flutter run -d chrome
