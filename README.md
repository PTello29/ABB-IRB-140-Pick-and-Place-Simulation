# Tutorial: Código en python del Pick And Place usando el ABB IRB 140

En esta parte del manual explicamos el funcionamiento e implementación del script en Python diseñado para ejecutar la secuencia de Pick and Place en el simulador Gazebo usando el robot ABB IRB 140 modelado y explicado en las anteriores partes de este manual. El script se apoya en el servicio de ROS “/gazebo/set_model_configuration” para modificar directamente las posiciones articulares del robot.

---
## 📋 Requisitos Previos

- Haber seguido el tutorial de *Configuración del Entorno* de ROS y Gazebo.

---
## 💾 Script

El script que hace funcionar la simulación del Pick And Place es el siguiente:

```
#!/usr/bin/env python3
import rospy
import time
from gazebo_msgs.srv import SetModelConfiguration

def move_robot(joint_positions):
    try:
        set_config = rospy.ServiceProxy('/gazebo/set_model_configuration', SetModelConfiguration)
        joint_names = ['joint_1', 'joint_2', 'joint_3', 'joint_4', 'joint_5', 'joint_6']
        
        resp = set_config(
            model_name='irb140',
            urdf_param_name='robot_description',
            joint_names=joint_names,
            joint_positions=joint_positions
        )
        
        if resp.success:
            rospy.loginfo("¡Movimiento completado!")
        else:
            rospy.logerr("Error al mover el robot: " + resp.status_message)
    
    except rospy.ServiceException as e:
        rospy.logerr("Error en el servicio: %s" % e)

def pick_and_place_sequence():
    # Home position
    home = [0.0, 0.0, 0.0, 0.0, 0.0, 3.14]
    home_a_pre1 = [0.1, 0.1, -0.03, 0.0, -0.1, 3.14]
    home_a_pre2 = [0.2, 0.2, -0.05, 0.0, -0.2, 3.14]
    home_a_pre3 = [0.3, 0.3, -0.08, 0.0, -0.3, 3.14]
    home_a_pre4 = [0.4, 0.4, -0.1, 0.0, -0.4, 3.14]
    home_a_pre5 = [0.5, 0.5, -0.13, 0.0, -0.5, 3.14]
    home_a_pre6 = [0.6, 0.6, -0.15, 0.0, -0.6, 3.14]
    home_a_pre7 = [0.7, 0.7, -0.18, 0.0, -0.7, 3.14]
    home_a_pre8 = [0.8, 0.8, -0.2, 0.0, -0.8, 3.14]
    home_a_pre9 = [0.9, 0.9, -0.25, 0.0, -0.9, 3.14]
    home_a_pre10 = [0.95, 0.95, -0.28, 0.0, -0.95, 3.14]
    # Posición sobre el objeto a recoger
    pre_pick = [1.0, 1.0, -0.3, 0.0, -1.0, 3.14]
    pre_a_pick1 = [1.0, 1.0, -0.28, 0.0, -1.0, 3.14]
    pre_a_pick2 = [1.0, 1.0, -0.25, 0.0, -1.0, 3.14]
    pre_a_pick3 = [1.0, 1.0, -0.23, 0.0, -1.0, 3.14]
    pre_a_pick4 = [1.0, 1.0, -0.2, 0.0, -1.0, 3.14]
    pre_a_pick5 = [1.0, 1.0, -0.18, 0.0, -1.0, 3.14]
    pre_a_pick6 = [1.0, 1.0, -0.15, 0.0, -1.0, 3.14]
    pre_a_pick7 = [1.0, 1.0, -0.13, 0.0, -1.0, 3.14]
    pre_a_pick8 = [1.0, 1.0, -0.1, 0.0, -1.0, 3.14]
    pre_a_pick9 = [1.0, 1.0, -0.08, 0.0, -1.0, 3.14]
    pre_a_pick10 = [1.0, 1.0, -0.05, 0.0, -1.0, 3.14]
    pre_a_pick11 = [1.0, 1.0, -0.03, 0.0, -1.0, 3.14]
    # Posición para recoger (con gripper abierto)
    pick = [1.0, 1.0, 0.0, 0.0, -1.0, 3.14]
    pick_a_post1 = [1.0, 1.0, -0.03, 0.0, -1.0, 3.14]
    pick_a_post2 = [1.0, 1.0, -0.05, 0.0, -1.0, 3.14]
    pick_a_post3 = [1.0, 1.0, -0.08, 0.0, -1.0, 3.14]
    pick_a_post4 = [1.0, 1.0, -0.1, 0.0, -1.0, 3.14]
    pick_a_post5 = [1.0, 1.0, -0.13, 0.0, -1.0, 3.14]
    pick_a_post6 = [1.0, 1.0, -0.15, 0.0, -1.0, 3.14]
    pick_a_post7 = [1.0, 1.0, -0.18, 0.0, -1.0, 3.14]
    pick_a_post8 = [1.0, 1.0, -0.2, 0.0, -1.0, 3.14]
    pick_a_post9 = [1.0, 1.0, -0.23, 0.0, -1.0, 3.14]
    pick_a_post10 = [1.0, 1.0, -0.25, 0.0, -1.0, 3.14]
    pick_a_post11 = [1.0, 1.0, -0.28, 0.0, -1.0, 3.14]
    # Posición después de recoger (con gripper cerrado)
    post_pick = [1.0, 1.0, -0.3, 0.0, -1.0, 3.14]
    post_a_pre1 = [0.9, 1.0, -0.29, 0.0, -1.0, 3.14]
    post_a_pre2 = [0.8, 1.0, -0.27, 0.0, -1.0, 3.14]
    post_a_pre3 = [0.7, 1.0, -0.25, 0.0, -1.0, 3.14]
    post_a_pre4 = [0.6, 1.0, -0.23, 0.0, -1.0, 3.14]
    post_a_pre5 = [0.5, 1.0, -0.22, 0.0, -1.0, 3.14]
    post_a_pre6 = [0.4, 1.0, -0.2, 0.0, -1.0, 3.14]
    post_a_pre7 = [0.3, 1.0, -0.19, 0.0, -1.0, 3.14]
    post_a_pre8 = [0.2, 1.0, -0.17, 0.0, -1.0, 3.14]
    post_a_pre9 = [0.0, 1.0, -0.15, 0.0, -1.0, 3.14]
    post_a_pre10 = [-0.1, 1.0, -0.13, 0.0, -1.0, 3.14]
    post_a_pre11 = [-0.2, 1.0, -0.12, 0.0, -1.0, 3.14]
    post_a_pre12 = [-0.3, 1.0, -0.1, 0.0, -1.0, 3.14]
    post_a_pre13 = [-0.4, 1.0, -0.09, 0.0, -1.0, 3.14]
    post_a_pre14 = [-0.5, 1.0, -0.07, 0.0, -1.0, 3.14]
    post_a_pre15 = [-0.6, 1.0, -0.05, 0.0, -1.0, 3.14]
    post_a_pre16 = [-0.7, 1.0, -0.04, 0.0, -1.0, 3.14]
    post_a_pre17 = [-0.8, 1.0, -0.03, 0.0, -1.0, 3.14]
    post_a_pre18 = [-0.9, 1.0, -0.02, 0.0, -1.0, 3.14]
    # Posición sobre el lugar donde soltar
    pre_place = [-1.0, 1.0, 0.0, 0.0, -1.0, 3.14]
    pre_a_place = [-1.0, 1.0, 0.03, 0.0, -1.0, 3.14]
    pre_a_place2 = [-1.0, 1.0, 0.05, 0.0, -1.0, 3.14]
    pre_a_place3 = [-1.0, 1.0, 0.08, 0.0, -1.0, 3.14]
    pre_a_place4 = [-1.0, 1.0, 0.1, 0.0, -1.0, 3.14]
    pre_a_place5 = [-1.0, 1.0, 0.13, 0.0, -1.0, 3.14]
    pre_a_place6 = [-1.0, 1.0, 0.15, 0.0, -1.0, 3.14]
    pre_a_place7 = [-1.0, 1.0, 0.18, 0.0, -1.0, 3.14]
    pre_a_place8 = [-1.0, 1.0, 0.2, 0.0, -1.0, 3.14]
    pre_a_place9 = [-1.0, 1.0, 0.23, 0.0, -1.0, 3.14]
    pre_a_place10 = [-1.0, 1.0, 0.25, 0.0, -1.0, 3.14]
    pre_a_place11 = [-1.0, 1.0, 0.28, 0.0, -1.0, 3.14]
    # Posición para soltar (con gripper abierto)
    place = [-1.0, 1.0, 0.3, 0.0, -1.0, 3.14]
    place_a_post1 = [-1.0, 0.95, 0.28, 0.0, -1.0, 3.14]
    place_a_post2 = [-1.0, 0.9, 0.24, 0.0, -1.0, 3.14]
    place_a_post3 = [-1.0, 0.85, 0.2, 0.0, -1.0, 3.14]
    place_a_post4 = [-1.0, 0.8, 0.18, 0.0, -1.0, 3.14]
    place_a_post5 = [-1.0, 0.75, 0.14, 0.0, -1.0, 3.14]
    place_a_post6 = [-1.0, 0.7, 0.1, 0.0, -1.0, 3.14]
    place_a_post7 = [-1.0, 0.65, 0.09, 0.0, -1.0, 3.14]
    place_a_post8 = [-1.0, 0.6, 0.05, 0.0, -1.0, 3.14]
    place_a_post9 = [-1.0, 0.55, 0.03, 0.0, -1.0, 3.14]
    # Posición para subir poco después de soltar
    post_place = [-1.0, 0.5, 0.0, 0.0, -1.0, 3.14]
    post_home1 = [-1.0, 0.5, 0.0, 0.0, -1.0, 3.14]
    post_home2 =[-0.9, 0.5, 0.0, 0.0, -0.9, 3.14]
    post_home3 =[-0.82, 0.48, 0.0, 0.0, -0.9, 3.14]
    post_home4 =[-0.74, 0.45, 0.0, 0.0, -0.81, 3.14]
    post_home5 =[-0.67, 0.42, 0.0, 0.0, -0.73, 3.14]
    post_home6 =[-0.61, 0.39, 0.0, 0.0, -0.66, 3.14]
    post_home7 =[-0.55, 0.35, 0.0, 0.0, -0.59, 3.14]
    post_home8 =[-0.49, 0.32, 0.0, 0.0, -0.53, 3.14]
    post_home9 =[-0.44, 0.29, 0.0, 0.0, -0.47, 3.14]
    post_home10 =[-0.39, 0.26, 0.0, 0.0, -0.42, 3.14]
    post_home11 =[-0.35, 0.23, 0.0, 0.0, -0.38, 3.14]
    post_home12 =[-0.31, 0.20, 0.0, 0.0, -0.34, 3.14]
    post_home13 =[-0.27, 0.18, 0.0, 0.0, -0.30, 3.14]
    post_home14 =[-0.24, 0.15, 0.0, 0.0, -0.26, 3.14]
    post_home15 =[-0.21, 0.13, 0.0, 0.0, -0.23, 3.14]
    post_home16 =[-0.18, 0.11, 0.0, 0.0, -0.20, 3.14]
    post_home17 =[-0.15, 0.09, 0.0, 0.0, -0.17, 3.14]
    post_home18 =[-0.13, 0.07, 0.0, 0.0, -0.14, 3.14]
    post_home19 =[-0.11, 0.06, 0.0, 0.0, -0.12, 3.14]
    post_home20 =[-0.09, 0.04, 0.0, 0.0, -0.10, 3.14]
    post_home21 =[-0.07, 0.03, 0.0, 0.0, -0.08, 3.14]
    post_home22 =[-0.05, 0.02, 0.0, 0.0, -0.06, 3.14]
    post_home23 =[-0.03, 0.01, 0.0, 0.0, -0.04, 3.14]
    post_home24 =[-0.01, 0.00, 0.0, 0.0, -0.02, 3.14]
    post_home25 =[0.0, 0.0, 0.0, 0.0, 0.0, 3.14]
    # Secuencia de movimientos
    rospy.loginfo("Moviendo a posición HOME")
    move_robot(home)
    time.sleep(0.1)
    
    move_robot(home_a_pre1)
    time.sleep(0.1)
    
    move_robot(home_a_pre2)
    time.sleep(0.1)
    
    move_robot(home_a_pre3)
    time.sleep(0.1)
    
    move_robot(home_a_pre4)
    time.sleep(0.1)
    
    move_robot(home_a_pre5)
    time.sleep(0.1)
    
    move_robot(home_a_pre6)
    time.sleep(0.1)
    
    move_robot(home_a_pre7)
    time.sleep(0.1)
    
    move_robot(home_a_pre8)
    time.sleep(0.1)
    
    move_robot(home_a_pre9)
    time.sleep(0.1)
    
    move_robot(home_a_pre10)
    time.sleep(0.1)
    
    rospy.loginfo("Moviendo a posición PRE-PICK")
    move_robot(pre_pick)
    time.sleep(0.2)
    
    move_robot(pre_a_pick1)
    time.sleep(0.2)
    
    move_robot(pre_a_pick2)
    time.sleep(0.2)
    
    move_robot(pre_a_pick3)
    time.sleep(0.2)
    
    move_robot(pre_a_pick4)
    time.sleep(0.2)
    
    move_robot(pre_a_pick5)
    time.sleep(0.2)
    
    move_robot(pre_a_pick6)
    time.sleep(0.2)
    
    move_robot(pre_a_pick7)
    time.sleep(0.2)
    
    move_robot(pre_a_pick8)
    time.sleep(0.2)
    
    move_robot(pre_a_pick9)
    time.sleep(0.2)
    
    move_robot(pre_a_pick10)
    time.sleep(0.2)
    
    move_robot(pre_a_pick11)
    time.sleep(0.2)
    
    rospy.loginfo("Moviendo a posición PICK")
    move_robot(pick)
    time.sleep(0.1)
    
    # Aquí iría el comando para cerrar el gripper
    rospy.loginfo("Cerrando gripper (simulado)")
    time.sleep(0.1)
    
    move_robot(pick_a_post1)
    time.sleep(0.1)
    
    move_robot(pick_a_post2)
    time.sleep(0.1)
    
    move_robot(pick_a_post3)
    time.sleep(0.1)
    
    move_robot(pick_a_post4)
    time.sleep(0.1)
    
    move_robot(pick_a_post5)
    time.sleep(0.1)
    
    move_robot(pick_a_post6)
    time.sleep(0.1)
    
    move_robot(pick_a_post7)
    time.sleep(0.1)
    
    move_robot(pick_a_post8)
    time.sleep(0.1)
    
    move_robot(pick_a_post9)
    time.sleep(0.1)
    
    move_robot(pick_a_post10)
    time.sleep(0.1)
    
    move_robot(pick_a_post11)
    time.sleep(0.1)
    
    rospy.loginfo("Moviendo a posición POST-PICK")
    move_robot(post_pick)
    time.sleep(0.1)
    
    move_robot(post_a_pre1)
    time.sleep(0.1)
    
    move_robot(post_a_pre2)
    time.sleep(0.1)
    
    move_robot(post_a_pre3)
    time.sleep(0.1)
    
    move_robot(post_a_pre4)
    time.sleep(0.1)
    
    move_robot(post_a_pre5)
    time.sleep(0.1)
    
    move_robot(post_a_pre6)
    time.sleep(0.1)
    
    move_robot(post_a_pre7)
    time.sleep(0.1)
    
    move_robot(post_a_pre8)
    time.sleep(0.1)
    
    move_robot(post_a_pre9)
    time.sleep(0.1)
    
    move_robot(post_a_pre10)
    time.sleep(0.1)
    
    move_robot(post_a_pre11)
    time.sleep(0.1)
    
    move_robot(post_a_pre12)
    time.sleep(0.1)
    
    move_robot(post_a_pre13)
    time.sleep(0.1)
    
    move_robot(post_a_pre14)
    time.sleep(0.1)
    
    move_robot(post_a_pre15)
    time.sleep(0.1)
    
    move_robot(post_a_pre16)
    time.sleep(0.1)
    
    move_robot(post_a_pre17)
    time.sleep(0.1)
    
    move_robot(post_a_pre18)
    time.sleep(0.1)
    
    rospy.loginfo("Moviendo a posición PRE-PLACE")
    move_robot(pre_place)
    time.sleep(0.2)
    
    move_robot(pre_a_place)
    time.sleep(0.2)
    
    move_robot(pre_a_place2)
    time.sleep(0.2)
    
    move_robot(pre_a_place3)
    time.sleep(0.2)
    
    move_robot(pre_a_place4)
    time.sleep(0.2)
    
    move_robot(pre_a_place5)
    time.sleep(0.2)
    
    move_robot(pre_a_place6)
    time.sleep(0.2)
    
    move_robot(pre_a_place7)
    time.sleep(0.2)
    
    move_robot(pre_a_place8)
    time.sleep(0.2)
    
    move_robot(pre_a_place9)
    time.sleep(0.2)
    
    move_robot(pre_a_place10)
    time.sleep(0.2)
    
    move_robot(pre_a_place11)
    time.sleep(0.2)
    
    rospy.loginfo("Moviendo a posición PLACE")
    move_robot(place)
    time.sleep(0.1)
    
    move_robot(place_a_post1)
    time.sleep(0.1)
    
    move_robot(place_a_post2)
    time.sleep(0.1)
    
    move_robot(place_a_post3)
    time.sleep(0.1)
    
    move_robot(place_a_post4)
    time.sleep(0.1)
    
    move_robot(place_a_post5)
    time.sleep(0.1)
    
    move_robot(place_a_post6)
    time.sleep(0.1)
    
    move_robot(place_a_post7)
    time.sleep(0.1)
    
    move_robot(place_a_post8)
    time.sleep(0.1)
    
    move_robot(place_a_post9)
    time.sleep(0.1)
    
    # Aquí iría el comando para abrir el gripper
    rospy.loginfo("Abriendo gripper (simulado)")
    time.sleep(0.1)
    
    rospy.loginfo("Moviendo a posición POS_PLACE")
    move_robot(post_place)
    time.sleep(0.1)
    
    move_robot(post_home1)
    time.sleep(0.1)
    
    move_robot(post_home2)
    time.sleep(0.1)
    
    move_robot(post_home3)
    time.sleep(0.1)
    
    move_robot(post_home4)
    time.sleep(0.1)
    
    move_robot(post_home5)
    time.sleep(0.1)
    
    move_robot(post_home6)
    time.sleep(0.1)
    
    move_robot(post_home7)
    time.sleep(0.1)
    
    move_robot(post_home8)
    time.sleep(0.1)
    
    move_robot(post_home9)
    time.sleep(0.1)
    
    move_robot(post_home10)
    time.sleep(0.1)
    
    move_robot(post_home11)
    time.sleep(0.1)
    
    move_robot(post_home12)
    time.sleep(0.1)
    
    move_robot(post_home13)
    time.sleep(0.1)
    
    move_robot(post_home14)
    time.sleep(0.1)
    
    move_robot(post_home15)
    time.sleep(0.1)
    
    move_robot(post_home16)
    time.sleep(0.1)
    
    move_robot(post_home17)
    time.sleep(0.1)
    
    move_robot(post_home18)
    time.sleep(0.1)
    
    move_robot(post_home19)
    time.sleep(0.1)
    
    move_robot(post_home20)
    time.sleep(0.1)
    
    move_robot(post_home21)
    time.sleep(0.1)
    
    move_robot(post_home22)
    time.sleep(0.1)
    
    move_robot(post_home23)
    time.sleep(0.1)
    
    move_robot(post_home24)
    time.sleep(0.1)
    
    move_robot(post_home25)
    time.sleep(0.1)
    
    rospy.loginfo("Moviendo a posición HOME")
    move_robot(home)

if __name__ == '__main__':
    try:
        rospy.init_node('irb140_pick_and_place')
        pick_and_place_sequence()
    except rospy.ROSInterruptException:
        pass
```
Puede encontrarse de igual forma en *src/abb_irb140_support/scripts/mover_irb140.py*

---
## 🛠️ Descripción del Código

El script puede resumirse en dos funciones principales:

* pick_and_place_sequence():

Esta función es la principal del código y arranca estableciendo los valores articulares de los motores en posiciones como *“home”, “pre_pick”, “pick”, “post_pick”, “pre_place”, “place”* y *“post_place”*. Estas posiciones de ancla fueron calculadas según nuestras necesidades, pero el usuario puede modificar las posiciones a su conveniencia alterando los valores de cada posición. Igualmente se incluyeron posiciones intermedias para trazar la trayectoria de movimiento. Estos valores pueden ser calculados en programas como *MATLAB* con la función *jtraj* del *Robotics Toolbox de Peter Corke*. Esto arroja matrices de transformación homogéneas entre una posición y la siguiente (por ejemplo, entre *home* y *pre_pick*) y se puede alterar la cantidad de matrices que se desean. Para cambiar los valores de la matriz de transformación homogénea a valores DH (los cuales son los que se usan como valores en las posiciones) se usa la función *ikine6s* (solamente para robots de 6 GDL, ya que para otro tipo de robot solo es *ikine*) igualmente del *Robotics Toolbox de Peter Corke*, el cual permite calcular la cinemática inversa y obtener estos valores.

**NOTA**: el *Robotics Toolbox de Peter Corke* te permite calcular la cinemática inversa y obtener los valores DH pero estos valores no siempre pueden coincidir con los valores establecidos en las posiciones, por lo que el usuario puede experimentar que el modelo tenga movimientos bruscos en las trayectorias. Es decir, que las articulaciones del robot se vean afectadas y muestren una configuración completamente distinta a la que se quiere, por lo que es recomendable modificar los valores o bien, calcular los propios valores DH. 

* move_robot(joint_positions):

Esta función dentro de *pick_and_place_sequence()* permite mover las articulaciones del robot y mostrarlas en la simulación. En la parte *a)* se explicó sobre cómo se establecieron los valores, por lo que en esta parte se mandan a llamar las posiciones para mostrarlas en la simulación. Hay que notar que después de cada función *move_robot()* se tiene un *time.sleep()* el cual puede ser modificado a gusto del usuario. Esta función solo hará que se espere un momento en que pase de una posición articular a la siguiente.

---
## 🏗️ Instrucciones
Para una correcta ejecución, se recomienda seguir lo siguiente:
**Paso 1:** Asegúrese de que el simulador Gazebo esté corriendo y el modelo del robot esté cargado.
**Paso 2:** Ejecute el script en una terminal con el entorno de ROS correctamente inicializado.
**Paso 3:** Use el comando: ./mover_irb140.py o rosrun abb_irb140_support mover_irb140.py si se quiere iniciar el paquete de ROS.
**Paso 4:** Observe cómo el robot ejecuta el movimiento predefinido.

---
## ✅ Conclusión

Resumen de lo aprendido y posibles extensiones o proyectos relacionados.

---

## 📬 Contacto

Para preguntas o sugerencias:

* 📧 Correo electrónico: ejemplo@correo.com
---