# SIMULADOR-GRAFICO-DE-CIRCUITOS-ELECTRONICOS-
Proyecto final de POO-BMA15-Grupo 3

    from tkinter import *
    from tkinter import ttk
    from tkinter import messagebox  # Para mostrar ventanas emergentes
    import os
    import math  # Para cálculos que requieran π y otras funciones matemáticas
    class Aplicacion:
    def __init__(self):
        self.usuarios_registrados = {}
        self.cargar_usuarios()
        self.login = Tk()
        self.configurar_login()
        
    def cargar_usuarios(self):
        if os.path.exists("usuarios.txt"):
            with open("usuarios.txt", "r") as file:
                for linea in file:
                    usuario, contraseña = linea.strip().split(",")
                    self.usuarios_registrados[usuario] = contraseña


    def guardar_usuarios(self):
        with open("usuarios.txt", "w") as file:
            for usuario, contraseña in self.usuarios_registrados.items():
                file.write(f"{usuario},{contraseña}\n")


    def configurar_login(self):
        self.login.title("Inicio de sesión")
        self.login.geometry("600x900")
        self.login.configure(bg="#004830")


        # Widgets de login
        Label(self.login, text="Usuario", fg="white", bg="#004830", font=("Arial", 14)).pack(pady=10)
        self.entry_usuario = Entry(self.login, font=("Arial", 14))
        self.entry_usuario.pack(pady=5)


        Label(self.login, text="Contraseña", fg="white", bg="#004830", font=("Arial", 14)).pack(pady=10)
        self.entry_contraseña = Entry(self.login, show="*", font=("Arial", 14))
        self.entry_contraseña.pack(pady=5)


        Button(self.login, text="Iniciar sesión", font=("Arial", 14), command=self.iniciar_session).pack(pady=5)


        self.etiqueta_error = Label(self.login, text="", fg="red", bg="#004830", font=("Arial", 11))
        self.etiqueta_error.pack(pady=5)


        Button(self.login, text="Registrar nuevo usuario", font=("Arial", 14),
               command=self.abrir_ventana_registro).pack(pady=5)


        self.login.mainloop()


    def iniciar_session(self):
        usuario = self.entry_usuario.get().strip()
        password = self.entry_contraseña.get().strip()


        if usuario in self.usuarios_registrados and self.usuarios_registrados[usuario] == password:
            self.login.destroy()
            self.mostrar_ventana_principal(usuario)
        else:
            self.etiqueta_error.config(text="Usuario o contraseña incorrectos", fg="red")


    def mostrar_ventana_principal(self, usuario):
        self.raiz = Tk()
        self.raiz.title(f"Simulador de circuito - {usuario}")
        self.raiz.geometry("1920x1200")
        self.raiz.configure(bg="#004830")


        # Barra de herramientas
        barra_tareas = Frame(self.raiz, bg="#97aeab", height=50)
        barra_tareas.grid(row=0, column=0, columnspan=2, sticky="ew")


        # Botones para componentes del circuito
        componentes = [
            ("Resistencia", self.calcular_resistencia),
            ("Fuente de Voltaje", self.calcular_fuente_voltaje),
            ("Capacitor", self.calcular_capacitor),
            ("Inductor", self.calcular_inductor)
        ]


        for i, (texto, comando) in enumerate(componentes):
            Button(barra_tareas, text=texto, font=("Arial", 12), command=comando).grid(row=0, column=i, padx=5, pady=5)


        # Panel de parámetros
        panel_parametros = Frame(self.raiz, bg="#a7ae97", width=200)
        panel_parametros.grid(row=1, column=0, sticky="ns")


        parametros = [
            ("Voltaje (V):", "entry_voltaje"),
            ("Corriente (A):", "entry_corriente"),
            ("Resistencia (Ω):", "entry_resistencia"),
            ("Capacitancia (F):", "entry_capacitancia"),
            ("Inductancia (H):", "entry_inductancia"),
            ("Frecuencia (Hz):", "entry_frecuencia")
        ]


        Label(panel_parametros, text="Parámetros del Circuito", bg="#a7ae97",
              font=("Arial", 14, "bold")).pack(pady=10)


        for texto, var_name in parametros:
            Label(panel_parametros, text=texto, bg="#a7ae97", font=("Arial", 12)).pack(pady=5)
            entry = Entry(panel_parametros, font=("Arial", 12))
            entry.pack(pady=5)
            setattr(self, var_name, entry)


        # Área de dibujo
        canvas = Canvas(self.raiz, bg="#151313", width=1920, height=1200)
        canvas.grid(row=1, column=1, sticky="nsew")


        # Grid de fondo
        for x in range(0, 1920, 20):
            canvas.create_line(x, 0, x, 1200, fill="#7d7a7a", width=1)
        for y in range(0, 1200, 20):
            canvas.create_line(0, y, 1920, y, fill="#7d7a7a", width=1)


        # Barra inferior
        Frame(self.raiz, bg="#97aeab", height=100).grid(row=2, column=0, columnspan=2, sticky="ew")


        self.raiz.grid_rowconfigure(1, weight=1)
        self.raiz.grid_columnconfigure(1, weight=1)
        self.raiz.mainloop()


    def calcular_resistencia(self):
        try:
            voltaje = float(self.entry_voltaje.get())
            corriente = float(self.entry_corriente.get())
            resistencia = voltaje / corriente
            messagebox.showinfo("Resistencia", f"La resistencia es: {resistencia:.2f} Ω")
        except ValueError:
            messagebox.showerror("Error", "Ingresa valores válidos para voltaje y corriente.")


    def calcular_fuente_voltaje(self):
        try:
            corriente = float(self.entry_corriente.get())
            resistencia = float(self.entry_resistencia.get())
            voltaje = corriente * resistencia
            messagebox.showinfo("Fuente de Voltaje", f"El voltaje es: {voltaje:.2f} V")
        except ValueError:
            messagebox.showerror("Error", "Ingresa valores válidos para corriente y resistencia.")


    def calcular_capacitor(self):
        try:
            capacitancia = float(self.entry_capacitancia.get())
            voltaje = float(self.entry_voltaje.get())
            energia = 0.5 * capacitancia * (voltaje ** 2)
            messagebox.showinfo("Capacitor", f"La energía almacenada es: {energia:.2f} J")
        except ValueError:
            messagebox.showerror("Error", "Ingresa valores válidos para capacitancia y voltaje.")


    def calcular_inductor(self):
        try:
            inductancia = float(self.entry_inductancia.get())
            corriente = float(self.entry_corriente.get())
            energia = 0.5 * inductancia * (corriente ** 2)
            messagebox.showinfo("Inductor", f"La energía almacenada es: {energia:.2f} J")
        except ValueError:
            messagebox.showerror("Error", "Ingresa valores válidos para inductancia y corriente.")


    def abrir_ventana_registro(self):
        ventana_registro = Toplevel()
        ventana_registro.title("Registro")
        ventana_registro.geometry("500x400")
        ventana_registro.configure(bg="#004830")


        # Widgets de registro
        Label(ventana_registro, text="Nuevo usuario:", fg="white", bg="#004830",
              font=("Arial", 14)).pack(pady=10)
        entry_usuario = Entry(ventana_registro, font=("Arial", 14))
        entry_usuario.pack(pady=5)


        Label(ventana_registro, text="Contraseña", fg="white", bg="#004830",
              font=("Arial", 14)).pack(pady=10)
        entry_contraseña = Entry(ventana_registro, show="*", font=("Arial", 14))
        entry_contraseña.pack(pady=4)


        etiqueta_error_registro = Label(ventana_registro, text="", fg="red", bg="#004830", font=("Arial", 11))
        etiqueta_error_registro.pack(pady=5)


        Button(ventana_registro, text="Registrar", font=("Arial", 14),
               command=lambda: self.registrar_usuario(
                   entry_usuario.get().strip(),
                   entry_contraseña.get().strip(),
                   ventana_registro,
                   etiqueta_error_registro
               )).pack(pady=20)


    def registrar_usuario(self, usuario, contraseña, ventana, etiqueta_error):
        if not usuario or not contraseña:
            etiqueta_error.config(text="Todos los campos son obligatorios", fg="red")
            return


        if usuario in self.usuarios_registrados:
            etiqueta_error.config(text="El usuario ya existe", fg="red")
        else:
            self.usuarios_registrados[usuario] = contraseña
            self.guardar_usuarios()
            ventana.destroy()
            self.etiqueta_error.config(text="Registro exitoso!", fg="green")
        if __name__ == "__main__":
            app = Aplicacion()




