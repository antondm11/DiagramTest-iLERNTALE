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

Playing --> GameOver : defeatPlayer()

%% Playing: Estado jugando - Llevará a Pausa al pulsar Esc (cambio a estado Paused)
Playing --> Paused : pause()

%% Pausa: Seleccionar opción "Salir", que lleva de vuelta al Menú principal
Paused --> Menu: selectPauseOptionQuit()

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

``` mermaid

graph LR
%% DIAGRAMA DE CASOS DE USO - USAR OBJETO EN COMBATE

%% Definir Actores
Player((Player))
Foe((Foe))

%% Definir límite del Sistema y Acciones
subgraph "iLERNTALE Combat"
CU1([Use Battle Item])
CU2([Select Item Option])
CU3([Select Item])
CU4([Engage In Battle])
CU5([Pick Up Item])
CU6([Await Player's Decision])


%% Definir relaciones especiales (include y extend)

%% Relación include (obligatoria). Hay que seleccionar un objeto para usarlo
CU1 -.->|&lt;&lt;include&gt;&gt;| CU3

%% Relación include (obligatoria). Para seleccionar un objeto, hay que seleccionar la opción Item
CU3 -.->|&lt;&lt;include&gt;&gt;| CU2

%% Relación include (obligatoria). Para seleccionar la opción item
CU2 -.->|&lt;&lt;include&gt;&gt;| CU4

%% Para usar un objeto, es obligatorio haberlo recogido del mapa antes de entrar en combate
CU4 -.->|&lt;&lt;include&gt;&gt;| CU5

end

%% Definir relaciones Actor/Casos de Uso
Player --- CU1
%% Mientras el jugador utiliza un objeto, el enemigo no ataca, espera que tome la decisión de usar objeto, atacar u otra
Foe --- CU6

```


# DIAGRAMA DE SECUENCIA


``` mermaid

sequenceDiagram
autonumber
%% Diagrama de Secuencia para el proceso de atacar y restar vida en iLERNTALE

%% Actores y participantes
actor Player
%% Paneles participantes

%%  Fight Button activa el minijuego de combate
participant FightButton
%% El minijuego es donde transcurre el combate
participant BattleMinigame
%% En el minijuego se restará vida según condiciones
%% Enemigo
participant Foe

%% Secuencia
%% El Jugador entra en combate con el enemigo y selecciona el botón de Luchar (Fight)
Player->>FightButton: selectFightButton()
activate FightButton
%% Al activarse el botón Fight, se genera el Minijuego y se activa éste y después la pelea con el enemigo
FightButton->>BattleMinigame: generateMinigame()
activate BattleMinigame
BattleMinigame->>: Foe: triggerFoeCombat()
activate Foe
%% Dentro del minijuego se resta vida según las siguientes condiciones
%% Si el jugador contacta con los puños verdes, inflige daño al enemigo y le resta vida
alt player collects green fists
BattleMinigame->>Foe: FoeTakesDamage
Foe-->>BattleMinigame: retrieveFoeRemainingHP()
%% Si el jugador contacta con las calaveras rojas, será el enemigo quien le inflija daño
else player collects red skulls
BattleMinigame->>Foe: FoeInflictsDamage
Foe-->>BattleMinigame: retrievePlayerRemainingHP()
end

deactivate Foe
BattleMinigame-->> FightButton: resetMinigame()
deactivate BattleMinigame
FightButton-->> Player: endFightTurn()
deactivate FightButton


```




