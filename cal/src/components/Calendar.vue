<template>
  <div class="calendar">
    <h2>{{ currentMonthName }} - {{ currentYear }}</h2>

    <div class="calendar-grid">
      <div class="calendar-header">
        <span v-for="(day, index) in daysOfWeek" :key="index">{{ day }}</span>
      </div>

      <div class="calendar-days">
        <div 
          v-for="(day, index) in daysInMonth" 
          :key="index"
          class="calendar-day" 
          :class="{ 'has-event': day.events && day.events.length > 0 }"
          @click="editEvent(day)">
          <span>{{ day.date || '' }}</span>
          <div v-if="day.events && day.events.length" class="event-indicator"></div>
        </div>
      </div>
    </div>

    <div v-if="selectedDay" class="event-modal">
      <div class="modal-content">
        <h3>Eventos de {{ selectedDay.date }}</h3>
        <input v-model="newEvent" placeholder="Adicionar evento" />
        <button @click="addEvent">Adicionar Evento</button>

        <ul>
          <li v-for="(event, index) in selectedDay.events" :key="index">
            {{ event }} <button @click="removeEvent(index)">Remover</button>
          </li>
        </ul>

        <button @click="closeModal">Fechar</button>
      </div>
    </div>
  </div>
</template>

<script>
export default {
  name: 'Calendar',
  data() {
    return {
      currentYear: new Date().getFullYear(),
      currentMonthIndex: new Date().getMonth(), // 0-11
      currentMonthName: new Date().toLocaleString('default', { month: 'long' }),
      daysOfWeek: ['Dom', 'Seg', 'Ter', 'Qua', 'Qui', 'Sex', 'Sáb'],
      daysInMonth: [], // inicializa vazio, vai preencher no created()
      selectedDay: null,
      newEvent: '',
    };
  },
  created() {
    // Preenche os dias depois que a instância existe
    this.daysInMonth = this.getDaysInMonth();
  },
  methods: {
    getDaysInMonth() {
      const days = [];
      const year = this.currentYear;
      const month = this.currentMonthIndex; // 0-based
      const firstDayDate = new Date(year, month, 1);
      const firstDay = firstDayDate.getDay(); // 0 (Dom) - 6 (Sáb)
      const lastDate = new Date(year, month + 1, 0).getDate(); // último dia do mês

      // placeholders para alinhar o primeiro dia
      for (let i = 0; i < firstDay; i++) {
        days.push({ date: null, events: [] });
      }

      // dias do mês
      for (let d = 1; d <= lastDate; d++) {
        days.push({ date: d, events: [] });
      }

      return days;
    },
    editEvent(day) {
      // não abre modal para células vazias (placeholders)
      if (!day || !day.date) return;
      // copiamos o objeto para evitar mutações acidentais (opcional)
      this.selectedDay = day;
    },
    addEvent() {
      if (this.newEvent && this.selectedDay) {
        // garante que events exista
        if (!this.selectedDay.events) this.selectedDay.events = [];
        this.selectedDay.events.push(this.newEvent);
        this.newEvent = '';
        // opcional: fechar modal automaticamente - não fiz isso por ser UX preferencial
      }
    },
    removeEvent(index) {
      if (this.selectedDay && this.selectedDay.events) {
        this.selectedDay.events.splice(index, 1);
      }
    },
    closeModal() {
      this.selectedDay = null;
      this.newEvent = '';
    },
  },
};
</script>

<style scoped>
.calendar {
  text-align: center;
}

.calendar-grid {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  gap: 0;
}

.calendar-header {
  display: grid;
  grid-template-columns: repeat(7, 1fr);
  font-weight: bold;
}

.calendar-days {
  display: contents; /* para manter grid de 7 colunas por linha */
}

.calendar-day {
  padding: 10px;
  border: 1px solid #ccc;
  cursor: pointer;
  min-height: 60px;
}

.has-event {
  background-color: lightgreen;
}

.event-indicator {
  background-color: red;
  width: 8px;
  height: 8px;
  border-radius: 50%;
  margin-top: 5px;
}

.event-modal {
  background-color: rgba(0, 0, 0, 0.5);
  position: fixed;
  top: 0;
  left: 0;
  right: 0;
  bottom: 0;
  display: flex;
  justify-content: center;
  align-items: center;
  padding: 20px;
}

/* wrapper dentro do modal para centralizar e estilo */
.modal-content {
  background: white;
  padding: 20px;
  border-radius: 8px;
  min-width: 300px;
}

.event-modal input {
  padding: 10px;
  margin-bottom: 10px;
  border: 1px solid #ccc;
  width: 100%;
  box-sizing: border-box;
}

.event-modal button {
  padding: 10px;
  background-color: #3498db;
  color: white;
  border: none;
  border-radius: 5px;
  cursor: pointer;
  margin-right: 8px;
}

.event-modal button:hover {
  background-color: #2980b9;
}

.event-modal ul {
  list-style-type: none;
  padding: 0;
  margin-top: 10px;
}

.event-modal li {
  display: flex;
  justify-content: space-between;
  margin-bottom: 10px;
}
</style>