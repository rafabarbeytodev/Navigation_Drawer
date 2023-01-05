# Cómo codificar un Navigation Drawer para Android App

## Agrega el DrawerLayout y NavigationView

La documentación oficial dice lo siguiente sobre DrawerLayout:

DrawerLayout actúa como un contenedor de nivel superior para el contenido de la ventana que permite que las vistas 
interactivas del "drawer" se extraigan de uno o ambos bordes verticales de la ventana.

La documentación oficial dice lo siguiente sobre NavigationView:

***NavigationView representa un menú de navegación estándar para la aplicación. El contenido del menú se puede rellenar con un archivo de recursos de menú.***

Para incluir los elementos de menú para el navigation drawer, podemos usar el atributo app:menu con un valor que apunta a un archivo de recursos de menú.
```
 <android.support.design.widget.NavigationView
        app:menu="@menu/activity_main_drawer" />
```
Aquí hemos definido un menú utilizando el < menu > que sirve como un contenedor para los elementos de menú. Un < item > crea un MenuItem, 
que representa un solo elemento en un menú.

A continuación, definimos nuestro primer grupo de menús utilizando el <group> archivo. Un < group > sirve como un contenedor invisible para los <item> 
elementos de menú en nuestro caso. Cada uno de los elementos < item >  tiene un id, un icono y untítulo. 
Ten en cuenta que se dibujará una línea horizontal al final de cada  < grupo >  para nosotros cuando se muestra en el navigation drawer.

Un < item > también puede contener un elemento anidado para crear un < menu > submenú, hicimos esto en nuestro último < item >. 
Observa que este último < item > tiene un título de propiedad .

Ten en cuenta que al mostrar los elementos de la lista de navegación de un recurso de menú, podríamos usar un ListView en su lugar. 
Pero, al configurar el navigation drawer con un recurso de menú, obtenemos el estilo de diseño de material en el navigation drawer de forma gratuita. 
Si usaste un *ListView*, tendrías que mantener la lista y también aplicarle estilo para cumplir con las especificaciones de diseño de material recomendadas 
para el navigation drawer.
        
## Inicialización de componentes

El *ActionBarDrawerToggle* configura el icono de la aplicación ubicado a la izquierda de la barra de acciones o la barra de herramientas para abrir y 
cerrar el cajón de navegación. Para poder crear una instancia de *ActionBarDrawerToggle*, tenemos que proporcionar los siguientes parámetros:

- *Un contexto padre, por ejemplo, en una actividad se usa esto, mientras que en un fragmento se llama a getActivity()*
- *Una instancia del widget DrawerLayout para vincular a Actionbar de la actividad*
- *El icono que se coloca encima del icono de la aplicación para indicar que hay un botón de alternancia*
- *Los recursos de cadena para las operaciones abierto y cerrado respectivamente (para accesibilidad)*
- *Invocamos el método addDrawerListener() en un DrawerLayout para conectar un ActionBarDrawerToggle con un DrawerLayout.*

Ten en cuenta que también habilitamos el icono de la aplicación a través de *setHomeButtonEnabled()* y lo habilitamos para la navegación "ascendente" 
a través de *setDisplayHomeAsUpEnabled()*.

Luego reenviamos los métodos de devolución de llamada de actividad *onPostCreate()*, *onConfigurationChanged()* y *onOptionsItemSelected()* al conmutador

Esto es lo que hace *syncState()*, según la documentación oficial:

***Sincroniza el estado del indicador del drawer/funcionamiento vinculado con el DrawerLayout ... 
Esto debe ser llamado desde el método onPostCreate de tu Actividad para sincronizar después de que el estado de la instancia del DrawerLayout haya sido restaurado,
y en cualquier otro momento cuando el estado pueda haber divergido de tal manera que el ActionBarDrawerToggle no haya sido notificado.
(Por ejemplo, si dejas de reenviar los eventos de drawer apropiados durante un período de tiempo).***

## Controlar los eventos con un clic

Ahora, veamos cómo controlar los eventos, para cada uno de los elementos en el navigation drawer. 
Ten en cuenta que al hacer clic en cualquier elemento se supone que te llevará a una nueva actividad o fragmento, es por eso que se llama un cajón de navegación!

En primer lugar, la actividad debe implementar *NavigationView.OnNavigationItemSelectedListener*.
```
class MainActivity : AppCompatActivity(), NavigationView.OnNavigationItemSelectedListener {
    // ... 
}
```

Este método se invoca cuando se selecciona un elemento en el menú de navegación. 
Usamos la expresión cuando para realizar diferentes acciones en función del elemento de menú en el que se hizo clic: los identificadores de elemento de menú 
sirven como constantes para la expresión cuando.

A continuación, tenemos que iniciar nuestro *NavigationView* y establecer este detector dentro de *onCreate()* de nuestra actividad.
```
class MainActivity : AppCompatActivity(), NavigationView.OnNavigationItemSelectedListener {
    // ... 
    override fun onCreate(savedInstanceState: Bundle?) {
        // ... 
        val navigationView: NavigationView = findViewById(R.id.nav_view)
        navigationView.setNavigationItemSelectedListener(this)
        // ... 
    }
// ...
```
Al hacer clic en algunos elementos, se muestra un mensaje del sistema, justo lo que esperábamos. 
Pero recuerda que al hacer clic en un elemento debe llevar al usuario a una nueva actividad o fragmento (ignoramos esto aquí por la brevedad).

Notarás que cuando haces clic en un elemento, el cajón todavía permanece. Sería mejor que lo cerraras automáticamente cada vez que se hiciera clic en un elemento. 
Para cerrar el cajón después de que hayas hecho clic en un vínculo, simplemente invoque *closeDrawer()*
en una instancia de *DrawerLayout* y pase *GravityCompat.START* al método.

## Manejo del botón Atrás presionado

Cuando el cajón está abierto, sería una mejor experiencia de usuario no cerrar la actividad de inicio si se presiona el botón Atrás. 
Esta es la forma en que funcionan las aplicaciones populares como la aplicación Bandeja de entrada de Google.

Por lo tanto, cuando el cajón está abierto y se presiona el botón Atrás, solo cierra el cajón en lugar de la actividad actual de inicio. 
A continuación, si el usuario vuelve a presionar el botón Atrás, se debe cerrar la actividad principal.
En el OnCreate() añadiremos:

```
onBackPressedDispatcher.addCallback(this /* lifecycle owner */, object : OnBackPressedCallback(true) {
            override fun handleOnBackPressed() {
                if (drawer.isDrawerOpen(GravityCompat.START)) {
                    drawer.closeDrawer(GravityCompat.START)
                } else {
                    finish()
                }
            }
        })
```


