# DiagramTest-iLERNTALE


# DIAGRAMA DE ESTADOS

``` mermaid

stateDiagram-v2
%% Diagrama de Estados del juego iLERNTALE

%% Se inicia el juego y va al Menú Principal
[*] --> Menu
%%% En el Menú puede elegir varias opciones

%% Playing: Estado jugando - Se cuenta la parte del prólogo
Menu --> Playing: selectOptionPlay()

%% Quit: Estado de cerrar el juego
Menu --> Quit: selectOptionQuit()

%% Playing: Estado jugando - Llevará a "Game Beaten" si se pasa el juego 
Playing --> GameBeaten : beatGame()

%% Playing: Estado jugando - Llevará a "Game Over" si muere 

Playing --> GameOver : playerDies()

%% Playing: Estado jugando - Llevará a Pausa al pulsar Esc (cambio a estado Paused)
Playing --> Paused : pause()

%% Pausa: Seleccionar opción "Salir", que lleva de vuelta al Menú principal
Paused --> Menu: selectOptionQuit()

%% Pausa: Seleccionar opción "Reanudar", que devuelve al juego (mapa o combate)
Paused --> Playing : selectOptionResume()


GameBeaten --> [*]
GameOver --> [*]
Quit--> [*]


```


# DIAGRAMAS DE CASOS DE USO

CASO DE USO - INICIAR PARTIDA

``` mermaid

graph LR
%% DIAGRAMA DE CASOS DE USO - INICIAR PARTIDA

%% Definir Actores
Player((Player))

%% Definir límite del Sistema y Acciones
subgraph "iLERNTALE"
CU1([Play Game])
CU2([Select Play In Main Menu])
CU3([Select Character])
CU4([Skip Prologue])

%% Definir relaciones especiales (include y extend)

%% Relación include (obligatoria). Es obligatorio seleccionar Personaje para Iniciar Partida
CU1 -.->|&lt;&lt;include&gt;&gt;| CU3

%% Relación include (obligatoria). Es obligatorio pulsar Play para Iniciar Partida (y seleccionar personaje)
CU3 -.->|&lt;&lt;include&gt;&gt;| CU2

%% Relación extend (opcional). Es opcional saltar el prólogo al Iniciar Partida
CU4 -.->|&lt;&lt;extend&gt;&gt;| CU1

end

%% Definir relaciones Actor/Casos de Uso
Player --- CU1


```


CASO DE USO - ATACAR

``` mermaid

graph LR
%% DIAGRAMA DE CASOS DE USO - ATACAR

%% Definir Actores
Player((Player))
Foe((Foe))

%% Definir límite del Sistema y Acciones
subgraph "iLERNTALE Combat"
CU1([Attack])
CU2([Select Fight Option])
CU3([Engage In Battle])
CU4([Inflict Damage])
CU5([Dodge Enemy Attacks])
CU6([Foe Attack])

%% Definir relaciones especiales (include y extend)

%% Relación include (obligatoria). Hay que entrar en combate para Atacar
CU1 -.->|&lt;&lt;include&gt;&gt;| CU2

%% Relación include (obligatoria). Hay que seleccionar opción Luchar (Fight) para Atacar
CU2 -.->|&lt;&lt;include&gt;&gt;| CU3


%% Relación extend (opcional). Aunque para infligir daño hay que atacar, en la pantalla de atacar es opcional infligir daño, ya que se puede fallar el minijuego que daña al enemigo (no recoger los puños verdes), aunque recomendable
CU1 -.->|&lt;&lt;extend&gt;&gt;| CU4

%% Relación extend (opcional). Es opcional esquivar los ataques del enemigo para Atacar (aunque recomendable)
CU1 -.->|&lt;&lt;extend&gt;&gt;| CU5

end

%% Definir relaciones Actor/Casos de Uso
Player --- CU1
Foe --- CU6

```





CASO DE USO - UTILIZAR OBJETO EN COMBATE







