# crud-angular-postgres
Aplicación web para insertar, editar, eliminar y visualizar registros almacenados en una base de datos PostgreSQL utilizando Angular para el frontend.

## 1. creacion de base de datos 
se crea una base de datos en postgreSQL y se crea la tabla donde estaran los datos de los usuarios:

    CREATE TABLE usuarios (
    id SERIAL PRIMARY KEY,
    nombre VARCHAR(100),
    correo VARCHAR(100),
    edad INT
    );

Una vez creada la tabla se realizan diferentes sentencias para verificar que la base de datos funciones correctamente:

    /*INSERTAR*/
    insert into usuarios (nombre , correo, edad) values ('maicol', 'maicolarevalo0701@gmail.com', 24);
    
    /*MOSTRAR*/
    select * from usuarios;
    
    /*ACTUALIZAR*/
    UPDATE usuarios set nombre = 'marlon' , correo = 'zumakill321@gmail.com' , edad = 28 WHERE usuarios.id = 1;
    
    /*ELIMINAR*/
    DELETE FROM usuarios WHERE usuarios.id = 1;

    /*MOSTRAR*/
    select * from usuarios
    
## 2. Crear proyecto en Apache NetBeans (JAVA):
Despues de crear la base de datos y la tabla en PostgreSQL, se crea un proyecto en JAVA para realizar la conexion con la base de datos:

  - En la creacion del proyecto en JAVA se genera el main donde implementaremos la conexion de la base de datos como tambien la verificacion de la misma, tambien tenemos que agregar las dependencias de PostgreSQL.

        package com.mycompany.crud_usuarios;
            / *se importan estas librerias para realizar la conexion a la base de datos (PostgreSQL) */
        import java.sql.Connection;
        import java.util.List;
    
        /**
         *
         * @author maicol
         */
        public class Crud_usuarios {
        
            public static void main(String[] args) {
              
              /*realizar conexion a la base de datos*/
              Conexion conexion = new Conexion();
              Connection conn = conexion.establecerConexion();
            
              /*se realiza un ciclo para verificar que si se realizo la conxion a la base de datos*/
              if (conn != null) {
                  
                  System.out.println("conexion exitosa");
                  
              } else {
                  
                  System.out.println("error de conexion");
                  
              }
        
        
            }
        }

- Se crea una clase donde se va a realizar la conexion con la base de datos.

      package com.mycompany.crud_usuarios;
      /*se importan estas librerias para realizar la conexion a la base de datos (PostgreSQL) */
      import java.sql.Connection;
      import java.sql.DriverManager;
      
      /**
       *
       * @author maicol
       */
      public class Conexion {
          
          Connection conectar = null;
          /*se agregan las credenciales para realizar la conexion */
          String usuario = "postgres";
          String contrasena = "123456lo";
          String database = "bdusuarios";
          String ip = "localhost";
          String puerto = "5432";
          
          String cadena = "jdbc:postgresql://" + ip + ":" +puerto+ "/" +database;
          
          /*funcion para poder abrir la conexion*/
          public Connection establecerConexion (){
              
              try {
                  
                  Class.forName("org.postgresql.Driver");
                  
                  conectar = DriverManager.getConnection(cadena, usuario, contrasena);
                  System.out.println("conexion de base de datos correctamente" + database);
                  
              } catch (Exception e) {
                  
                  System.out.println("error de conexion" + e.getMessage());
              }
              
              return conectar; 
          }
             
      }
  
- Despues se crea otra clase donde se le agregaran los atributos que van a tener nuestros usuarios.
 
        package com.mycompany.crud_usuarios;
        
        /**
         *
         * @author maicol
         */
        public class CUsuarios {
            
                /*los datos que continene la tabla de base de datos*/
            int id;
            String nombre;
            String correo;
            int edad;
            
            
            /*construtor vacio es obligatorio si se usa frameworks o lectura de base de datos*/
            public CUsuarios(){}
            
            /*el constructor sin ID ya que es para insertar nuevos usuarios o personas*/
            public CUsuarios (String nombre, String correo, int edad){
                
                this.nombre = nombre;
                this.correo = correo;
                this.edad = edad;
         
            }
            
            /*constructor completo para poder leer y actualizar los datos de las personas o usuarios*/
            public CUsuarios (int id, String nombre, String correo, int edad){
                
                this.id = id;
                this.nombre = nombre;
                this.correo = correo;
                this.edad = edad;
             
            }
        
            /*generamos los getter and setter*/
            public int getId() {
                return id;
            }
        
            public void setId(int id) {
                this.id = id;
            }
        
            public String getNombre() {
                return nombre;
            }
        
            public void setNombre(String nombre) {
                this.nombre = nombre;
            }
        
            public String getCorreo() {
                return correo;
            }
        
            public void setCorreo(String correo) {
                this.correo = correo;
            }
        
            public int getEdad() {
                return edad;
            }
        
            public void setEdad(int edad) {
                this.edad = edad;
            }
            
            public String toString(){
                return "Usuario{" + "id = " +id+ ",nombre = " +nombre+ ",correo = " +correo+ ",edad = " +edad+ "}";
            }
            
        }
- Se crea otra clase donde se implementaran las funciones del crud (crear,leer,actualizar,borrar) para poder modificar la bse de datos.

      package com.mycompany.crud_usuarios;
      /*se importan estas librerias para realizar la conexion a la base de datos (PostgreSQL) */
      import java.sql.*;
      /*se importa esta libreria para la implementacion concreta de una interfaz lista*/
      import java.util.ArrayList;
      /*se importa esta libreria para la representacion de un lista ordenada de elementos*/
      import java.util.List;
      
      /**
       *
       * @author maicol
       */
      public class CUsuariosDAO {
          
              /*crear una objeto con la clase conexion*/
          Conexion conexion = new Conexion();
          
          /*insertar persona a la base de datos*/
          public void insertarUsuario (CUsuarios usuarios) {
              
              String sql = "INSERT INTO usuarios (nombre, correo, edad) VALUES (?, ?, ?)";
              
              try (Connection conn = conexion.establecerConexion();
                      PreparedStatement ps = conn.prepareStatement(sql)){
                  
                  /*datos que toca ingresar para agregar una persona*/
                  ps.setString(1, usuarios.getNombre());
                  ps.setString(2, usuarios.getCorreo());
                  ps.setInt(3, usuarios.getEdad());
                  
                  /*actualizar la base de datos*/
                  ps.executeUpdate();
                  
                  /*imprimir si se agrego correctamente*/
                  System.out.println("usuario ingresado correctamente");
                 
                  
              } catch (SQLException e) {
                  
                  System.out.println("error al insertar" + e.getMessage());
                  
              }
          }
          
          /*mostrar todas las personas de la base de datos de usuarios*/
          public List<CUsuarios> listarUsuario (){
              
              List<CUsuarios> lista = new ArrayList<>();
              String sql = "SELECT * FROM usuarios";
              
              
              try (Connection conn = conexion.establecerConexion();
                   Statement st = conn.createStatement();
                   ResultSet rs = st.executeQuery(sql)) {
                  
                  while (rs.next()){
                      
                      CUsuarios personas = new CUsuarios (
                      
                      rs.getInt("Id"),
                      rs.getString("nombre"),
                      rs.getString("correo"),
                      rs.getInt("edad")
                    
                      );
                      lista.add(personas);
                  }
                  
              }catch (SQLException e){
                  
                  System.out.println("error al listar" + e.getMessage());
                  
              }
              
              return lista;  
          }
          
          /*modificar las personas de la base de datos usuarios*/
          public void actualizarUsuario (CUsuarios usuarios){
              
              String sql = "UPDATE usuarios SET nombre=?, correo=?, edad=? WHERE id=?";
              
              try (Connection conn = conexion.establecerConexion();
                   PreparedStatement ps = conn.prepareStatement(sql)){
                  
                  ps.setString(1, usuarios.getNombre());
                  ps.setString(2, usuarios.getCorreo());
                  ps.setInt(3, usuarios.getEdad());
                  ps.setInt(4, usuarios.getId());
                  
                  int filas = ps.executeUpdate();
                  
                  
                  /*utilizar un if para saber si se elimino o no la peronsa*/
                  if (filas > 0){
                      
                      System.out.println("eliminacion de persona correctamente");
                      
                  }else {
                      
                      System.out.println("persona con ID no encontrado");
                      
                  }
                  
              } catch (Exception e) {
                  
                  System.out.println("error en modificar: " + e.getMessage());
                  
              }
          }
          
          /*eliminacion de persona de la base de datos usurios*/
          public void eliminarUsuario (int id) {
              
              String sql= "DELETE FROM usuarios WHERE id=?";
              try (Connection conn = conexion.establecerConexion();
                   PreparedStatement ps = conn.prepareStatement(sql)){
                  
                  ps.setInt(1, id);
                  
                  int filas = ps.executeUpdate();
                  
                  if (filas > 0){
                      System.out.println("eliminacion de perona en la base de datos usuarios correctamente");
                  }else {
                      System.out.println("no se encontro la persona con ese ID");
                  }
                  
              }catch(SQLException e){
                  System.out.println("error al eliminar una persona" + e.getMessage());
              }
              
          }
          
      }





