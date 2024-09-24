Proyecto de Reservas - Documentación
Este proyecto es una aplicación web de reservas, diseñada para permitir a los usuarios seleccionar un día y una hora para realizar un trámite específico. Utiliza Angular para el frontend y Bootstrap para el diseño de la interfaz. No depende de un backend, y las reservas se gestionan utilizando localStorage para almacenar los datos de las citas.

Índice
Componentes Principales
ReservaComponent
CitasComponent
ReservaService
Funciones Clave
Abrir Modal
Guardar Reserva
Cancelar Reserva
Verificar Bloques Ocupados
Diseño y Estilos
Componentes Principales
ReservaComponent
Este componente es el encargado de la funcionalidad principal de la página de reservas. Permite al usuario seleccionar un día y un bloque de hora para realizar una reserva, así como rellenar los datos personales y el tipo de trámite.

Archivos
reserva.component.ts: Lógica y manejo de datos.
reserva.component.html: Plantilla del formulario de reserva.
Funcionalidades principales:
Selección de tipo de trámite (renovación, primera vez, cambio de datos).
Validación de campos de nombre, RUT y fecha de nacimiento.
Visualización de bloques horarios y control para que un usuario solo pueda seleccionar un bloque y un día.
Deshabilitación de los bloques ya reservados.
html
Copiar código
<div class="container mt-4">
  <h2>Reservar una Cita</h2>
  <form #reservaForm="ngForm" (ngSubmit)="abrirModal()">
    <!-- Formulario para los datos de la reserva -->
    ...
  </form>
</div>
CitasComponent
Este componente se utiliza para mostrar todas las citas reservadas por el usuario y le permite cancelarlas si lo desea. Además, se encarga de la lógica para verificar si una cita ya ha sido tomada y la deshabilita para que no pueda volver a seleccionarse.

Archivos
citas.component.ts: Manejo de la visualización y cancelación de las citas.
citas.component.html: Interfaz que muestra las citas existentes y el estado de las horas reservadas.
Funcionalidades principales:
Mostrar las citas del usuario.
Cancelar una reserva seleccionada.
Verificar las horas bloqueadas y reflejarlo en el calendario.
html
Copiar código
<div class="container mt-4">
  <h2>Tus Citas</h2>
  <div *ngFor="let cita of citas">
    <p>{{ cita.fecha }} - {{ cita.hora }}</p>
    <button (click)="cancelarCita(cita)">Cancelar</button>
  </div>
</div>
ReservaService
El ReservaService actúa como un servicio central para manejar las operaciones relacionadas con las reservas. Se utiliza para almacenar y recuperar las citas desde el localStorage.

Archivos
reserva.service.ts: Contiene métodos para guardar, obtener y cancelar reservas en el localStorage.
Funcionalidades principales:
Almacenar las reservas en localStorage.
Verificar la existencia de una reserva para un bloque horario específico.
Gestionar la cancelación de citas.
typescript
Copiar código
export class ReservaService {
  guardarReserva(reserva: any) {
    const reservas = this.obtenerReservas();
    reservas.push(reserva);
    localStorage.setItem('reservas', JSON.stringify(reservas));
  }

  obtenerReservas() {
    const reservas = localStorage.getItem('reservas');
    return reservas ? JSON.parse(reservas) : [];
  }

  cancelarReserva(fecha: string, hora: string) {
    let reservas = this.obtenerReservas();
    reservas = reservas.filter(
      (reserva) => reserva.fecha !== fecha || reserva.hora !== hora
    );
    localStorage.setItem('reservas', JSON.stringify(reservas));
  }
}
Funciones Clave
Abrir Modal
Esta función abre un modal de confirmación cuando el usuario ha completado todos los campos del formulario de reserva. Se asegura de que todos los datos sean correctos antes de proceder a guardar la cita.

typescript
Copiar código
abrirModal() {
  // Mostrar modal de confirmación antes de reservar
  const modal = new bootstrap.Modal(document.getElementById('modalConfirmacion'));
  modal.show();
}
Guardar Reserva
Esta función se encarga de verificar la disponibilidad de la cita y, si está disponible, la guarda en localStorage.

typescript
Copiar código
confirmarReserva() {
  const nuevaReserva = {
    nombre: this.nombre,
    rut: this.rut,
    fecha: this.diaSeleccionado,
    hora: this.horaSeleccionada,
    tipoTramite: this.tipoTramite
  };

  this.reservaService.guardarReserva(nuevaReserva);
  this.mensaje = 'Reserva realizada con éxito';
  this.reservaExitosa = true;
}
Cancelar Reserva
Permite al usuario cancelar una cita previamente tomada. La cita se elimina del localStorage y la interfaz se actualiza para reflejar el cambio.

typescript
Copiar código
cancelarCita(cita: any) {
  this.reservaService.cancelarReserva(cita.fecha, cita.hora);
  this.obtenerCitas(); // Actualizar la lista de citas
}
Verificar Bloques Ocupados
Esta función es utilizada para deshabilitar los bloques de horario que ya han sido reservados, evitando que otro usuario los seleccione.

typescript
Copiar código
isBloqueOcupado(bloque: string): boolean {
  const reservas = this.reservaService.obtenerReservas();
  return reservas.some(reserva => reserva.hora === bloque);
}
Diseño y Estilos
Uso de Bootstrap
Se ha integrado Bootstrap para mejorar el diseño y la presentación de la aplicación. Los formularios, botones y modales utilizan componentes de Bootstrap para garantizar que la aplicación sea responsiva y fácil de usar.

Formulario de reserva: Tiene un diseño limpio con campos de entrada alineados y mensajes de error para la validación.
Modales: El modal de confirmación se centra y permite al usuario confirmar o cancelar su acción antes de guardar una reserva.
Tabla de bloques horarios: Organiza los días de la semana en columnas y los bloques de tiempo en filas, permitiendo una visualización clara de las horas disponibles.
html
Copiar código
<div class="modal fade" id="modalConfirmacion" tabindex="-1" aria-labelledby="modalConfirmacionLabel" aria-hidden="true">
  <div class="modal-dialog">
    <div class="modal-content">
      <div class="modal-header">
        <h5 class="modal-title">Confirmar Reserva</h5>
        <button type="button" class="btn-close" data-bs-dismiss="modal" aria-label="Close"></button>
      </div>
      <div class="modal-body">
        <p>¿Está seguro de que desea confirmar la reserva?</p>
      </div>
      <div class="modal-footer">
        <button type="button" class="btn btn-secondary" data-bs-dismiss="modal">Cancelar</button>
        <button type="button" class="btn btn-success" (click)="confirmarReserva()">Confirmar</button>
      </div>
    </div>
  </div>
</div>
Mejoras Futuras
Algunas posibles mejoras incluyen:

Implementación de un backend para gestionar reservas de manera más robusta y persistente.
Autenticación de usuarios para que cada persona pueda gestionar sus reservas de forma segura.
Optimización del manejo de estados en el frontend para evitar colisiones en las reservas.

