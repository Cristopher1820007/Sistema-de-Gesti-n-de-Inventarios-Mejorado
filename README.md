# Sistema de Gestion-de Inventarios Mejorado
Mejorar el sistema de gestión de inventarios desarrollado anteriormente para que utilice archivos para almacenar y recuperar la información del inventario y maneje excepciones durante la lectura y escritura de archivos.
class Producto:
    def __init__(self, id_producto, nombre, cantidad, precio):
        self.id_producto = id_producto
        self.nombre = nombre
        self.cantidad = cantidad
        self.precio = precio

    def __str__(self):
        return f"{self.id_producto},{self.nombre},{self.cantidad},{self.precio}"


class Inventario:
    def __init__(self, archivo="inventario.txt"):
        self.archivo = archivo
        self.productos = {}
        self.cargar_inventario()

    def cargar_inventario(self):
        """Carga productos desde el archivo inventario.txt"""
        try:
            if not os.path.exists(self.archivo):
                # Si no existe, se crea vacío
                with open(self.archivo, "w") as f:
                    pass
            else:
                with open(self.archivo, "r") as f:
                    for linea in f:
                        try:
                            id_producto, nombre, cantidad, precio = linea.strip().split(",")
                            self.productos[id_producto] = Producto(id_producto, nombre, int(cantidad), float(precio))
                        except ValueError:
                            print("️ Error: línea corrupta en archivo, se omitió.")
        except FileNotFoundError:
            print(" Archivo no encontrado, se creará uno nuevo.")
        except PermissionError:
            print(" No tienes permisos para leer el archivo.")

    def guardar_inventario(self):
        """Guarda todos los productos en el archivo inventario.txt"""
        try:
            with open(self.archivo, "w") as f:
                for producto in self.productos.values():
                    f.write(str(producto) + "\n")
        except PermissionError:
            print(" No tienes permisos para escribir en el archivo.")

    def agregar_producto(self, id_producto, nombre, cantidad, precio):
        if id_producto in self.productos:
            print("️ El producto ya existe.")
        else:
            self.productos[id_producto] = Producto(id_producto, nombre, cantidad, precio)
            self.guardar_inventario()
            print(" Producto añadido y guardado en archivo.")

    def actualizar_producto(self, id_producto, cantidad=None, precio=None):
        if id_producto in self.productos:
            if cantidad is not None:
                self.productos[id_producto].cantidad = cantidad
            if precio is not None:
                self.productos[id_producto].precio = precio
            self.guardar_inventario()
            print(" Producto actualizado en archivo.")
        else:
            print("️ Producto no encontrado.")

    def eliminar_producto(self, id_producto):
        if id_producto in self.productos:
            del self.productos[id_producto]
            self.guardar_inventario()
            print("roducto eliminado del archivo.")
        else:
            print(" Producto no encontrado.")

    def mostrar_inventario(self):
        if not self.productos:
            print(" Inventario vacío.")
        else:
            for producto in self.productos.values():
                print(f"ID: {producto.id_producto}, Nombre: {producto.nombre}, "
                      f"Cantidad: {producto.cantidad}, Precio: {producto.precio}")


# Ejemplo de uso
if __name__ == "__main__":
    inventario = Inventario()

    inventario.agregar_producto("001", "Laptop", 10, 1200.50)
    inventario.agregar_producto("002", "Mouse", 50, 25.99)
    inventario.mostrar_inventario()

    inventario.actualizar_producto("001", cantidad=8)
    inventario.eliminar_producto("002")
    inventario.mostrar_inventario()
