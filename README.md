<!DOCTYPE html>
<html lang="en">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Grade Calculator</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 0;
            padding: 0;
            display: flex;
            justify-content: center;
            align-items: center;
            height: 100vh;
            background-color: #affcff;
        }

        .container {
            display: flex;
            justify-content: space-around;
            width: 100%;
            max-width: 1200px;
        }

        .box {
            display: flex;
            flex-direction: column;
            align-items: center;
            background-color: rgb(69, 152, 215);
            padding: 30px;
            border-radius: 10px;
            box-shadow: 0 0 15px rgba(0, 0, 0, 0.2);
            text-align: center;
            width: 300px;
        }

        input[type="number"],
        input[type="text"] {
            width: 100px;
            padding: 5px;
            font-size: 16px;
            margin: 10px;
        }

        button {
            padding: 10px 20px;
            font-size: 16px;
            margin-top: 10px;
            cursor: pointer;
            background-color: #874caf;
            color: white;
            border: none;
            border-radius: 5px;
        }

        .result {
            font-size: 18px;
            margin-top: 10px;
        }

        .graph-box {
            background-color: rgb(122, 192, 241);
            padding: 20px;
            border-radius: 10px;
            text-align: center;
            width: 300px;
            height: 300px;
            box-shadow: 0 0 10px rgba(0, 0, 0, 0.1);
        }

        .hidden {
            display: none;
        }
    </style>
</head>

<body>
    <div class="container">
        <!-- Box on the left (Graph for Passing) -->
        <div class="graph-box">
            <h3>Grades Needed to Pass</h3>
            <p>Midterm vs Final</p>
            <canvas id="graphPass"></canvas>
            <!-- Placeholder for Graph -->
        </div>

        <!-- Box in the Center (Main Calculator) -->
        <div class="box">
            <h1>Grade Calculator</h1>
            <label for="prelim">Prelim Grade: </label>
            <input type="number" id="prelim" placeholder="Enter Prelim Grade" step="0.1" min="0" max="100"><br>

            <button onclick="showResults()">Calculate</button>

            <!-- Results for passing and Dean's Lister -->
            <div class="result" id="resultPass"></div>
            <div class="result" id="resultDean"></div>
        </div>

        <!-- Box on the right (Graph for Dean's Lister) -->
        <div class="graph-box">
            <h3>Grades Needed for Dean's Lister</h3>
            <p>Midterm vs Final</p>
            <canvas id="graphDean"></canvas>
            <!-- Placeholder for Graph -->
        </div>
    </div>

    <script>
        function showResults() {
            var prelim = parseFloat(document.getElementById('prelim').value);

            // Validate prelim input
            if (isNaN(prelim) || prelim < 0 || prelim > 100) {
                alert("Please enter a valid Prelim grade between 1 and 100.");
                return;
            }

            // Calculate required final grades based on midterm grades for passing and Dean's Lister
            calculateFinals('pass');
            calculateFinals('dean');
        }

        function calculateFinals(type) {
            var prelim = parseFloat(document.getElementById('prelim').value);
            var data = [];

            // For passing
            if (type === 'pass') {
                var resultPass = '';
                for (var midterm = 16; midterm <= 100; midterm++) {
                    var finalPass = (75 - (prelim * 0.2) - (midterm * 0.3)) / 0.5;
                    var midpoint = (75 - 0.2 * prelim) / 0.8
                    if (finalPass >= 50 && finalPass <= 100) {
                        resultPass = `To pass, you need at least a midterm and a final grade of "${midpoint.toFixed(1)}".`;
                        data.push({ x: midterm, y: finalPass });
                    }
                }
                document.getElementById('resultPass').innerHTML = resultPass;
                updateGraph('pass', data);
            }

            // For Dean's Lister
            else if (type === 'dean') {
                var resultDean = '';
                for (var midterm = 66; midterm <= 100; midterm++) {
                    var finalDean = (90 - (prelim * 0.2) - (midterm * 0.3)) / 0.5;
                    var midpoint = (90 - 0.2 * prelim) / 0.8
                    if (finalDean >= 80 && finalDean <= 100) {
                        resultDean = `To be a Dean's Lister, you need at least a midterm and a final grade of "${midpoint.toFixed(1)}".`;
                        data.push({ x: midterm, y: finalDean });
                    }
                }

                if (!resultDean) {
                    resultDean = "Dean's Lister is not possible with this prelim grade.";
                }

                document.getElementById('resultDean').innerHTML = resultDean;
                updateGraph('dean', data);
            }
        }

        function updateGraph(type, data) {
            var graph = type === 'pass' ? graphPass : graphDean;
            graph.data.datasets[0].data = data;
            graph.update();
        }

        // Setup charts using Chart.js
        var graphPassCanvas = document.getElementById('graphPass').getContext('2d');
        var graphDeanCanvas = document.getElementById('graphDean').getContext('2d');

        var graphPass = new Chart(graphPassCanvas, {
            type: 'scatter',
            data: {
                datasets: [{
                    label: 'Grades Needed to Pass',
                    data: [],
                    backgroundColor: 'rgba(255, 99, 132, 0.2)',
                    borderColor: 'rgba(255, 99, 132, 1)',
                    borderWidth: 1
                }]
            },
            options: {
                scales: {
                    x: {
                        min: 16,
                        max: 100,
                        title: {
                            display: true,
                            text: 'Midterm Grade'
                        }
                    },
                    y: {
                        min: 50,
                        max: 100,
                        title: {
                            display: true,
                            text: 'Final Grade'
                        }
                    }
                }
            }
        });

        var graphDean = new Chart(graphDeanCanvas, {
            type: 'scatter',
            data: {
                datasets: [{
                    label: 'Grades Needed for Dean\'s Lister',
                    data: [],
                    backgroundColor: 'rgba(54, 162, 235, 0.2)',
                    borderColor: 'rgba(54, 162, 235, 1)',
                    borderWidth: 1
                }]
            },
            options: {
                scales: {
                    x: {
                        min: 66,
                        max: 100,
                        title: {
                            display: true,
                            text: 'Midterm Grade'
                        }
                    },
                    y: {
                        min: 80,
                        max: 100,
                        title: {
                            display: true,
                            text: 'Final Grade'
                        }
                    }
                }
            }
        });
    </script>
</body>

</html>
