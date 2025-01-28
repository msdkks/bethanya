

<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Gerenciador de Estudos</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      margin: 20px;
      padding: 0;
      background-color: #f4f4f4;
    }

    table {
      width: 100%;
      border-collapse: collapse;
      margin-top: 20px;
      background: white;
      box-shadow: 0 2px 5px rgba(0, 0, 0, 0.1);
    }

    th, td {
      padding: 10px;
      border: 1px solid #ddd;
      text-align: center;
    }

    th {
      background-color: #0073e6;
      color: white;
    }

    input, button {
      margin: 5px;
      padding: 8px;      border: 1px solid #ccc;      border-radius: 5px;
    }

    button {      background-color: #0073e6;      color: white;      cursor: pointer;
    }

    button:hover {      background-color: #005bb5;    }
  </style>
</head>
<body>
  <h1>Gerenciador de Estudos</h1>

  <form id="study-form">
    <label for="theme">Tema:</label>
    <input type="text" id="theme" required>

    <label for="dateWatched">Data da Aula:</label>
    <input type="date" id="dateWatched" required>

    <button type="button" onclick="addTheme()">Adicionar Tema</button>
  </form>

  <table id="study-table">
    <thead>
      <tr>
        <th>Tema</th>
        <th>Data da Aula</th>
        <th>1ª Revisão</th>
        <th>2ª Revisão</th>
        <th>3ª Revisão</th>
        <th>Percentual de Acertos</th>
        <th>Qtd. de Questões</th>
      </tr>
    </thead>
    <tbody>
      <!-- Rows dynamically added here -->
    </tbody>
  </table>

  <script>
    const themes = [];

    function addTheme() {
      const theme = document.getElementById('theme').value;
      const dateWatched = document.getElementById('dateWatched').value;

      if (!theme || !dateWatched) {
        alert('Por favor, preencha todos os campos!');
        return;
      }

      const firstReview = calculateNextDate(dateWatched, 21);
      const secondReview = calculateNextDate(firstReview, 14);
      const thirdReview = calculateNextDate(secondReview, 30);

      themes.push({
        theme,
        dateWatched,
        firstReview,
        secondReview,
        thirdReview,
        accuracy: null,
        questions: null,
      });

      updateTable();
    }

    function calculateNextDate(startDate, days) {
      const date = new Date(startDate);
      date.setDate(date.getDate() + days);
      return date.toISOString().split('T')[0];
    }

    function updatePerformance(index, accuracy) {
      const theme = themes[index];
      theme.accuracy = accuracy;
      theme.questions = calculateQuestions(accuracy);

      updateTable();
    }

    function calculateQuestions(accuracy) {
      if (accuracy >= 80) return 5;
      if (accuracy >= 50) return 10;
      return 15;
    }

    function updateTable() {
      const tableBody = document.getElementById('study-table').querySelector('tbody');
      tableBody.innerHTML = '';

      themes.forEach((theme, index) => {
        const row = document.createElement('tr');
        row.innerHTML = `
          <td>${theme.theme}</td>
          <td>${theme.dateWatched}</td>
          <td>${theme.firstReview}</td>
          <td>${theme.secondReview}</td>
          <td>${theme.thirdReview}</td>
          <td>
            <input 
              type="number" 
              min="0" max="100" 
              value="${theme.accuracy || ''}" 
              onchange="updatePerformance(${index}, this.value)"
              placeholder="%"
            >
          </td>
          <td>${theme.questions || 'N/A'}</td>
        `;
        tableBody.appendChild(row);
      });
    }
  </script>
</body>
</html>
