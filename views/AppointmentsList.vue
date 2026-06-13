<template>
  <div>
    <nav class="navbar navbar-light bg-white shadow-sm mb-4">
      <div class="container d-flex justify-content-between align-items-center">
        <span class="fw-bold fs-5">Doctor Appointments</span>
        <router-link to="/" class="btn btn-outline-primary">⇆ Switch Page</router-link>
      </div>
    </nav>

    <div class="container">
      <div class="card shadow-sm">
        <div class="card-header">
          <h5 class="mb-0">Appointments</h5>
        </div>

        <div class="card-body p-0">
          <table class="table table-bordered table-striped mb-0">
            <thead class="table-light">
              <tr>
                <th>Name</th>
                <th>Symptoms</th>
                <th>Time</th>
                <th>Status</th>
                <th>Update</th>
              </tr>
            </thead>

            <tbody>
              <tr v-for="appointment in appointments" :key="appointment.appointmentID">
                <td>{{ appointment.patientName }}</td>
                <td>{{ appointment.symptoms }}</td>
                <td>{{ appointment.slot }}</td>
                <td>{{ appointment.status }}</td>
                <td>
                  <select
                    class="form-select"
                    :value="appointment.status"
                    @change="e => updateStatus(appointment, e.target.value)"
                  >
                    <option>Pending</option>
                    <option>In Progress</option>
                    <option>Completed</option>
                  </select>
                </td>
              </tr>
            </tbody>

          </table>
        </div>
      </div>
    </div>
  </div>
</template>

<script>
const API_BASE_URL = "https://2ela1ukdsb.execute-api.eu-north-1.amazonaws.com";

export default {
  name: "AppointmentsList",

  data() {
    return {
      appointments: []
    };
  },

  mounted() {
    this.fetchAppointments();
  },

  methods: {
    fetchAppointments() {
      fetch(`${API_BASE_URL}/appointments`)
        .then(res => res.json())
        .then(data => {
          this.appointments = Array.isArray(data) ? data : JSON.parse(data.body);
        })
        .catch(err => {
          console.error("Failed to fetch appointments:", err);
        });
    },

    updateStatus(appointment, newStatus) {
      const appointmentId = appointment.appointmentID;

      const payload = {
        appointmentID: appointmentId,
        status: newStatus
      };

      fetch(`${API_BASE_URL}/appointments/${appointmentId}`, {
        method: "PATCH",
        headers: {
          "Content-Type": "application/json"
        },
        body: JSON.stringify(payload)
      })
        .then(async res => {
          const rawBody = await res.text();

          if (!res.ok) {
            throw new Error(`HTTP ${res.status}: ${rawBody}`);
          }

          return rawBody ? JSON.parse(rawBody) : {};
        })
        .then(() => {
          alert("Status updated!");
          this.fetchAppointments();
        })
        .catch(err => {
          console.error("Failed to update status:", err);
          alert("Update failed. See console for details.");
        });
    }
  }
};
</script>
