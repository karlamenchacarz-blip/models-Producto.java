public class Producto {
    private int idProducto;
    private String nombreComercial;
    private String marcaLaboratorio;
    private String formaFarmaceutica;
    private String viaAdministracion;
    private String presentacion;

    public Producto() {}

    public Producto(int idProducto, String nombreComercial, String marcaLaboratorio, 
                    String formaFarmaceutica, String viaAdministracion, String presentacion) {
        this.idProducto = idProducto;
        this.nombreComercial = nombreComercial;
        this.marcaLaboratorio = marcaLaboratorio;
        this.formaFarmaceutica = formaFarmaceutica;
        this.viaAdministracion = viaAdministracion;
        this.presentacion = presentacion;
    }

    public int getIdProducto() { return idProducto; }
    public void setIdProducto(int idProducto) { this.idProducto = idProducto; }

    public String getNombreComercial() { return nombreComercial; }
    public void setNombreComercial(String nombreComercial) { this.nombreComercial = nombreComercial; }

    public String getMarcaLaboratorio() { return marcaLaboratorio; }
    public void setMarcaLaboratorio(String marcaLaboratorio) { this.marcaLaboratorio = marcaLaboratorio; }

    public String getFormaFarmaceutica() { return formaFarmaceutica; }
    public void setFormaFarmaceutica(String formaFarmaceutica) { this.formaFarmaceutica = formaFarmaceutica; }

    public String getViaAdministracion() { return viaAdministracion; }
    public void setViaAdministracion(String viaAdministracion) { this.viaAdministracion = viaAdministracion; }

    public String getPresentacion() { return presentacion; }
    public void setPresentacion(String presentacion) { this.presentacion = presentacion; }


    public String String() {
        return "ID: " + idProducto + " | " + nombreComercial + " (" + marcaLaboratorio + ") - " + presentacion;
    }
}

package services;

import dao.ProductoDAO;
import models.Producto;
import java.util.List;

public class ProductoService {
    private ProductoDAO productoDAO = new ProductoDAO();

    public String registrarProducto(Producto p) {
        
        if (getNombreComercial().trim().isEmpty() || getMarcaLaboratorio().trim().isEmpty() || 
            getFormaFarmaceutica().trim().isEmpty() || getViaAdministracion().trim().isEmpty() || 
            getPresentacion().trim().isEmpty()) {
            return "⚠️ Error: Todos los campos son obligatorios.";
        }
        
        if (productoDAO.buscarPorNombreExacto(p.getNombreComercial())) {
            return "⚠️ Error: Ya existe un producto registrado con el nombre '" + p.getNombreComercial() + "'.";
        }

        boolean exito = productoDAO.agregar(p);
        return exito ? "✅ Producto agregado con éxito." : "❌ No se pudo agregar el producto de forma interna.";
    }

    public String modificarProducto(Producto p) {
        if (p.getNombreComercial().trim().isEmpty() || p.getMarcaLaboratorio().trim().isEmpty()) {
            return "⚠️ Error: El nombre y la marca no pueden quedar vacíos.";
        }
        
        boolean exito = productoDAO.editar(p);
        return exito ? "✅ Producto actualizado con éxito." : "❌ No se pudo actualizar el producto.";
    }

    public List<Producto> obtenerTodos() {
        return productoDAO.consultarTodos();
    }

    public List<Producto> filtrarPorNombre(String nombre) {
        return productoDAO.buscarPorNombre(nombre);
    }

    public String borrarProducto(int id) {
        boolean exito = productoDAO.eliminar(id);
        return exito ? "✅ Producto eliminado con éxito." : "❌ No se pudo eliminar el producto.";
    }
}

package menus;

import models.Producto;
import services.ProductoService;
import java.util.Scanner;
import java.util.List;

public class MenuAdministrador {
    private static ProductoService service = new ProductoService();
    private static Scanner sc = new Scanner(System.in);

    // Este método se manda llamar desde tu MenuAdministrador principal al seleccionar "Productos"
    public static void seccionProductos() {
        int opcion = -1;
        do {
            System.out.println("\n--- 📦 ADMINISTRACIÓN DE PRODUCTOS ---");
            System.out.println("1. Agregar Producto");
            System.out.println("2. Editar Producto");
            System.out.println("3. Eliminar Producto");
            System.out.println("4. Consultar Todos los Productos");
            System.out.println("5. Buscar Producto por Nombre");
            System.out.println("0. Volver al Menú Principal");
            System.out.print("Selecciona una opción: ");
            
            try {
                opcion = Integer.parseInt(sc.nextLine());
                switch (opcion) {
                    case 1: menuAgregar(); break;
                    case 2: menuEditar(); break;
                    case 3: menuEliminar(); break;
                    case 4: mostrarProductos(); break;
                    case 5: menuBuscar(); break;
                    case 0: System.out.println("Volviendo..."); break;
                    default: System.out.println("⚠️ Opción no válida.");
                }
            } catch (NumberFormatException e) {
                System.out.println("⚠️ Por favor, introduce un número válido.");
            }
        } while (opcion != 0);
    }

    private static void menuAgregar() {
        System.out.println("\n--- Agregar Nuevo Producto ---");
        System.out.print("Nombre Comercial: "); String nombre = sc.nextLine();
        System.out.print("Marca / Laboratorio: "); String marca = sc.nextLine();
        System.out.print("Forma Farmacéutica: "); String forma = sc.nextLine();
        System.out.print("Vía de Administración: "); String via = sc.nextLine();
        System.out.print("Presentación: "); String presentacion = sc.nextLine();

        Producto nuevo = new Producto(0, nombre, marca, forma, via, presentacion);
        String resultado = service.registrarProducto(nuevo);
        System.out.println(resultado);
    }

    private static void menuEditar() {
        System.out.println("\n--- Editar Producto ---");
        System.out.print("Introduce el ID del producto a editar: ");
        int id = Integer.parseInt(sc.nextLine());
        System.out.print("Nuevo Nombre Comercial: "); String nombre = sc.nextLine();
        System.out.print("Nueva Marca / Laboratorio: "); String marca = sc.nextLine();
        System.out.print("Nueva Forma Farmacéutica: "); String forma = sc.nextLine();
        System.out.print("Nueva Vía de Administración: "); String via = sc.nextLine();
        System.out.print("Nueva Presentación: "); String presentacion = sc.nextLine();

        Producto editado = new Producto(id, nombre, marca, forma, via, presentacion);
        String resultado = service.modificarProducto(editado);
        System.out.println(resultado);
    }

    private static void menuEliminar() {
        System.out.println("\n--- Eliminar Producto ---");
        System.out.print("Introduce el ID del producto a borrar: ");
        int id = Integer.parseInt(sc.nextLine());
        String resultado = service.borrarProducto(id);
        System.out.println(resultado);
    }

    private static void mostrarProductos() {
        System.out.println("\n--- Lista Completa de Productos ---");
        List<Producto> lista = service.obtenerTodos();
        if(lista.isEmpty()) System.out.println("No hay productos registrados.");
        else lista.forEach(System.out.println);
    }

    private static void menuBuscar() {
        System.out.println("\n--- Buscar Producto ---");
        System.out.print("Escribe el nombre (o parte del nombre): ");
        String busqueda = sc.nextLine();
        List<Producto> lista = service.filtrarPorNombre(busqueda);
        if(lista.isEmpty()) System.out.println("No se encontraron coincidencias.");
        else lista.forEach(System.out.println);
    }
}
