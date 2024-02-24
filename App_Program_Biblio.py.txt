import pickle

class Libro:
    def __init__(self, titulo, autor, cantidad=1):
        self.titulo = titulo
        self.autor = autor
        self.cantidad = cantidad
        self.disponible = True

class Usuario:
    def __init__(self, nombre):
        self.nombre = nombre
        self.libros_prestados = []

class Biblioteca:
    def __init__(self):
        self.libros = {}
        self.usuarios = {}

    def agregar_libro(self, titulo, autor):
        if titulo in self.libros:
            self.libros[titulo].cantidad += 1
        else:
            self.libros[titulo] = Libro(titulo, autor)

    def mostrar_libros(self):
        print("Lista de libros en la biblioteca:")
        for libro in self.libros.values():
            estado = "Disponible" if libro.disponible else "No Disponible"
            print(f"Título: {libro.titulo}, Autor: {libro.autor}, Cantidad: {libro.cantidad}, Estado: {estado}")

    def prestar_libro(self, titulo, nombre_usuario):
        if titulo in self.libros and self.libros[titulo].disponible:
            if nombre_usuario in self.usuarios:
                libro = self.libros[titulo]
                usuario = self.usuarios[nombre_usuario]
                usuario.libros_prestados.append(libro)
                libro.disponible = False
                libro.cantidad -= 1
                print(f"El libro '{titulo}' ha sido prestado a {nombre_usuario}.")
            else:
                print("Usuario no registrado.")
        else:
            print("El libro no está disponible.")

    def registrar_usuario(self, nombre):
        if nombre not in self.usuarios:
            self.usuarios[nombre] = Usuario(nombre)
            print(f"Usuario {nombre} registrado correctamente.")
        else:
            print("El usuario ya está registrado.")

    def guardar_datos(self, archivo):
        with open(archivo, 'wb') as f:
            pickle.dump((self.libros, self.usuarios), f)
        print("Datos de la biblioteca guardados correctamente.")

    def cargar_datos(self, archivo):
        try:
            with open(archivo, 'rb') as f:
                self.libros, self.usuarios = pickle.load(f)
            print("Datos de la biblioteca cargados correctamente.")
        except FileNotFoundError:
            print("El archivo de datos no existe. Inicializando biblioteca vacía.")

    def listar_usuarios(self):
        print("Lista de usuarios registrados:")
        for usuario in self.usuarios.keys():
            print(usuario)

    def listar_libros_usuario(self, nombre_usuario):
        if nombre_usuario in self.usuarios:
            libros_prestados = self.usuarios[nombre_usuario].libros_prestados
            if libros_prestados:
                print(f"Libros prestados a {nombre_usuario}:")
                for libro in libros_prestados:
                    print(f"Título: {libro.titulo}, Autor: {libro.autor}")
            else:
                print(f"{nombre_usuario} no tiene libros prestados.")
        else:
            print("Usuario no registrado.")

    def devolver_libro(self, titulo, nombre_usuario):
        if nombre_usuario in self.usuarios:
            usuario = self.usuarios[nombre_usuario]
            for libro in usuario.libros_prestados:
                if libro.titulo == titulo:
                    libro.disponible = True
                    libro.cantidad += 1
                    usuario.libros_prestados.remove(libro)
                    print(f"El libro '{titulo}' ha sido devuelto por {nombre_usuario}.")
                    return
            print(f"{nombre_usuario} no tiene el libro '{titulo}' prestado.")
        else:
            print("Usuario no registrado.")


def menu():
    print("\nMenú:")
    print("1. Agregar Libro")
    print("2. Mostrar Libros")
    print("3. Prestar Libro")
    print("4. Registrar Usuario")
    print("5. Guardar Datos")
    print("6. Cargar Datos")
    print("7. Listar Usuarios")
    print("8. Listar Libros de Usuario")
    print("9. Devolver Libro")
    print("0. Salir")

if __name__ == "__main__":
    biblioteca = Biblioteca()
    archivo = 'datos_biblioteca.pkl'
    biblioteca.cargar_datos(archivo)

    while True:
        menu()
        opcion = input("Seleccione una opción: ")

        if opcion == '1':
            titulo = input("Ingrese el título del libro: ")
            autor = input("Ingrese el autor del libro: ")
            biblioteca.agregar_libro(titulo, autor)
        elif opcion == '2':
            biblioteca.mostrar_libros()
        elif opcion == '3':
            titulo = input("Ingrese el título del libro: ")
            nombre_usuario = input("Ingrese el nombre del usuario: ")
            biblioteca.prestar_libro(titulo, nombre_usuario)
        elif opcion == '4':
            nombre_usuario = input("Ingrese el nombre del usuario: ")
            biblioteca.registrar_usuario(nombre_usuario)
        elif opcion == '5':
            biblioteca.guardar_datos(archivo)
        elif opcion == '6':
            biblioteca.cargar_datos(archivo)
        elif opcion == '7':
            biblioteca.listar_usuarios()
        elif opcion == '8':
            nombre_usuario = input("Ingrese el nombre del usuario: ")
            biblioteca.listar_libros_usuario(nombre_usuario)
        elif opcion == '9':
            titulo = input("Ingrese el título del libro: ")
            nombre_usuario = input("Ingrese el nombre del usuario: ")
            biblioteca.devolver_libro(titulo, nombre_usuario)
        elif opcion == '0':
            biblioteca.guardar_datos(archivo)
            print("¡Hasta luego!")
            break
        else:
            print("Opción no válida. Intente de nuevo.")
