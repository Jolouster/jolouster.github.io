---
layout: post
title: Crear, eliminar y volver a crear hilos en Qt/C++
meta: Aprende a crear correctamente hilos (threads), a eliminarlos y volver a crearlos con C++ y Qt.
category: Programación
author: Jonathan López
tag: C++
---


Estoy haciendo pruebas con C++ y Qt. Quiero hacer un programa que según los datos que encuentre en la base de datos cree X hilos para realizar cada uno de ellos un proceso laaaaargo. Este proceso se debe repetir indefinidamente mientras el usuario no lo detenga.

Para ello tiene dos opciones: 
* Detener el proceso temporalmente. Típico "start/stop". 
* Reiniciarlos.

Reiniciarlos tiene sentido cuando el usuarios ha modificado los datos almacenados en la base de datos o archivos (la configuración).

Es en esta opción en la que me voy a centrar en este artículo.

Si lo reinicia quiero eliminar todo. Eliminar los hilos creados de forma dinámica y los objetos que se han movido a estos hilos para realizar el trabajo. Después habría que volver a empezar el proceso desde cero.

Se que se podría decir al usuarios que cerrara la aplicación y volviera ha ejecutarla, pero no sería elegante. Quireo aprender a hacerlo "bien" y de paso lo comparto con todos vosotros.

Este es el código que empleo para las pruebas:

### Archivo *myWorker.h*

En este archivo tengo la declaración de la clase que se encarga de hacer "el trabajo duro" en cada *hilo* o *thread*.

    #ifndef MYWORKER_H
    #define MYWORKER_H

    #include <QCoreApplication>
    #include <QObject>
    #include <QDebug>

    namespace jlu
    {
        class myWorker : public QObject
        {
                Q_OBJECT

            public:
                explicit myWorker ();
                ~myWorker ();
                void setWorkerID (int workID);
            signals:
                void startProcess();
            public slots:
                void changeStatus (int id, bool newStatus);
                void process();    // <---- Aquí dentro se ejecutará todo lo que nos interesa.

            private:
                void loadConfigData();

                // Propiedades de la clase:
                bool isActive;
                int myID;
        };
    }


    #endif // MYWORKER_H

### Archivo *myWorker.cpp*

La implementación de la clase.

    #include "myworker.h"

    jlu::myWorker::myWorker()
    {
        connect (this, SIGNAL (startProcess()),
                this, SLOT (process()));
    }

    jlu::myWorker::~myWorker()
    {
        // Mensaje que uso para comprobar si se ha eliminado o no.
        qDebug() << "Se ha eliminado al hilo con ID: " << myID;
    }

    void jlu::myWorker::setWorkerID (int workID)
    {
        if (0 <= workID)
        {
            myID = workID;
        }
        else
        {
            myID = 0;
        }
    }

    void jlu::myWorker::changeStatus (int id, bool newStatus)
    {
        if (myID != id)
        {
            return;
        }

        isActive =  newStatus;

        if (isActive)
        {
            emit startProcess();
        }
    }

    void jlu::myWorker::process()
    {
        loadConfigData(); // Carga toda la información necesaria para funcionar.
        qDebug() << "Se inicia el proceso con id = " << myID;

        while (isActive)
        {
            // Un laaargo proceso que se repita indefinidamente

            // Aquí va el código que quiero que se ejecute indefinidamente hasta
            // que el usuario diga basta.
            
            // Atendemos los eventos que se hayan podido producir.
            QCoreApplication::processEvents (QEventLoop::AllEvents);
        }

        qDebug() << "Proceso detenido.";
    }

    void jlu::myWorker::loadConfigData()
    {
        // Carga datos desde la base de datos.
        // Carga datos de archivos de configuración etc.
    }

### Archivo *manageWorkers.h*

En este archivo tengo la declaración de la clase encargada de iniciar, controlar los *"threads"* y detenerlos cuando el usuario diga.

    #ifndef MANAGEWORKERS_H
    #define MANAGEWORKERS_H

    #include <QObject>
    #include <QThread>
    #include <QDebug>
    #include "myworker.h"

    namespace jlu
    {
        class manageWorkers : public QObject
        {
                Q_OBJECT
            public:
                explicit manageWorkers (QObject * parent = nullptr);
                ~manageWorkers();
            signals:
                void changeStatusOfWorker (int id, bool newStatus);
            public slots:
                void loadAndStartAllWorkers();
                void killallWorkers();
            private:
                int numWorkers;
                QThread * myThreads = NULL;
                myWorker * myProcess = NULL;
        };
    }
    #endif // MANAGEWORKERS_H

### Archivo *manageWorkers.cpp*

Su implementación.

    #include "manageworkers.h"

    jlu::manageWorkers::manageWorkers (QObject * parent) : QObject (parent)
    {

    }

    jlu::manageWorkers::~manageWorkers() {}


    void jlu::manageWorkers::loadAndStartAllWorkers()
    {
        numWorkers = 4; // Esto es realmente el resultado de una "futura" consulta SQL

        if (0 == numWorkers)
        {
            return;
        }

        myThreads = new QThread[numWorkers];
        myProcess = new jlu::myWorker[numWorkers];

        for (int i = 0; i < numWorkers; i++)
        {
            myProcess[i].setWorkerID (i); // Se tomará de la consulta realizada
            myProcess[i].moveToThread (&myThreads[i]);

            connect (this, SIGNAL (changeStatusOfWorker (int, bool)),
                    &myProcess[i], SLOT (changeStatus (int, bool)));
            // Demás conexiones necesarias

            myThreads[i].start();
            // Iniciamos el proceso.
            emit changeStatusOfWorker (i, true);
        }
    }

    void jlu::manageWorkers::killallWorkers()
    {
        qDebug() << " keys: " << netsConfigList.keys();

        for (int i = 0; i < numWorkers; i++)
        {
            qDebug() << "Se detiene el hilo n. " << i;
            emit changeStatusOfWorker (i, false);
            myThreads[i].quit();         // <---  El orden importa!! 
            myThreads[i].wait (1000);
        }

        // Ahora se eliminan los objetos y se libera la memoria correctamente.
        delete []myThreads;
        delete []myProcess;

        qDebug() << " Se han eliminado todos los hilos";
    }

Aquí es donde se produce el control de los hilos. Su inicio,  gestión y eliminación si así lo deseamos.

En el método `jlu::manageWorkers::loadAndStartAllWorkers()` es donde se crean tantos "hilos" como hagan falta y los objetos de la clase *"myWorker"* que quiero ejecutar en cada hilo.

Según muchos ejemplos y documentación en esta parte es donde también se añade la siguiente conexión:

    connect (&myThreads[i], SIGNAL (finished()), &myProcess[i], SLOT (deleteLater()));

Si añadimos esta conexión, no funcionará. De echo el programa se romperá. Tras hacer `myThreads[i].quit();` se lanzaría la señal que le diría al objeto `myProcess[i]` que se destruyese cuando pudiese... Esto parece que deja al objeto `myProcess[i]` medio colgado por ahí y da el fallo que comentaba.

### Archivo *main.cpp* para las pruebas

El siguiente archivo es donde pruebo el funcionamiento del codigo anterior. Simulo en envío las señales de inicio y paro que podría lanzar el usuario pulsando un botón de la interfaz gráfica.

    #include <QCoreApplication>
    #include <QTimer>
    #include <QObject>
    #include <QDebug>
    #include <QThread>
    #include <QDateTime>

    // Inserción de mis archivos.
    #include "manageworkers.h"

    #define DATETIME_FORMAT "yyyy-MM-dd HH:mm:ss"


    // Programa principal solo para testear el reinicio de los objetos.
    int main (int argc, char * argv[])
    {
        QCoreApplication a (argc, argv);

        jlu::manageWorkers * myControl = new jlu::manageWorkers();
        /*
         * El programa inicializa automáticamente los hilos y los procesos. Se ha elegido así.
         */
        myControl->loadAndStartAllWorkers();
        qDebug () << QDateTime::currentDateTime().toString (DATETIME_FORMAT);

        /*
         * A continuación se simula el envío de las señales de inicio y detención que podría 
         * lanzar el usuario desde la interfaz gráfica.
         */

        // Tras 10 segundos de funcionamiento detengo todos los hilos
        QTimer::singleShot (10000, myControl, SLOT (killallWorkers()));
        
        // Tras 5 segundos de la detención los vuelvo a iniciar
        QTimer::singleShot (15000, myControl, SLOT (loadAndStartAllWorkers()));

        // Tras 7 segundos del ultimo reinicio de los hilos, los vuelvo a eliminar
        QTimer::singleShot (23000, myControl, SLOT (killallWorkers()));

        /*
         * ---- fin de las pruebas ----
         */

        // Finalizamos el programa tras N seg.
        QTimer::singleShot (25000, &app, SLOT (quit()));

        return a.exec();
    }

¿Pensáis que se podría hacer de alguna otra manera más simple o mejor? ¿Qué opináis de este ejemplo? ¿Tenéis alguna duda? 

No dudéis en comentar.

P.D.: Si quisiéras simular el inicio/pausa del proceso que se ejecuta en los "hilos" sin eliminarlos, probad a lanzar la señal `changeStatusOfWorker()` de la clase `manageWorkers`.