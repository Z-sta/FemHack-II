# FemHack-II


 // Controller
app.controller('ChartController', function($scope) {
  $scope.chartData = [
    { year: 1990, users: 0.3 },
    // Add the rest of the data here
  ];
});

// HTML Template
<div ng-controller="ChartController">
  <div id="chartContainer"></div>
</div>

app.controller('ChartController', function($scope) {
  // Chart data
  $scope.chartData = [
    { year: 1990, users: 0.3 },
    // Add the rest of the data here
  ];

  // Three.js scene setup
  var scene = new THREE.Scene();
  var camera = new THREE.PerspectiveCamera(75, window.innerWidth / window.innerHeight, 0.1, 1000);
  var renderer = new THREE.WebGLRenderer();
  renderer.setSize(window.innerWidth, window.innerHeight);
  document.getElementById('chartContainer').appendChild(renderer.domElement);

  // Create chart points
  var chartPoints = $scope.chartData.map(function(data) {
    return new THREE.Vector3(data.year, data.users, 0);
  });

  // Create chart geometry
  var geometry = new THREE.BufferGeometry().setFromPoints(chartPoints);

  // Create chart material
  var material = new THREE.LineBasicMaterial({ color: 0x0000ff });

  // Create chart line
  var line = new THREE.Line(geometry, material);

  // Add chart line to the scene
  scene.add(line);

  // Set camera position
  camera.position.z = 5;

  // Render the scene
  function animate() {
    requestAnimationFrame(animate);
    renderer.render(scene, camera);
  }
  animate();
});

angular.module('myApp', [])
  .controller('ChartController', function($scope) {
    $scope.chartConfig = {
      type: 'bar',
      data: {
        labels: [], // Years
        datasets: [] // Data for each country
      },
      options: {
        scales: {
          x: {
            ticks: {
              autoSkip: false,
              maxRotation: 90,
              minRotation: 90
            }
          }
        },
        plugins: {
          title: {
            display: true,
            text: 'Top 10 Countries with the Largest Number of Internet Users'
          }
        }
      }
    };

    $scope.data = {
      1980: {
        'United States': 357000,
        'Canada': 15000,
        'Germany': 4500,
        // Add more data for other countries...
      },
      1981: {
        'United States': 567000,
        'Canada': 20000,
        'Germany': 8000,
        // Add more data for other countries...
      },
      // Add data for other years...
      2023: {
        'United States': 340000000,
        'China': 940000000,
        'India': 580000000,
        // Add more data for other countries...
      }
    };

    $scope.updateChart = function() {
      // Extract the top 10 countries for each year
      var top10Countries = {};
      angular.forEach($scope.data, function(countryData, year) {
        var sortedCountries = Object.entries(countryData)
          .sort(function(a, b) {
            return b[1] - a[1];
          })
          .slice(0, 10);
        top10Countries[year] = sortedCountries;
      });

      // Prepare chart data
      var years = Object.keys($scope.data);
      $scope.chartConfig.data.labels = years;
      $scope.chartConfig.data.datasets = [];

      angular.forEach(top10Countries, function(countryData, year) {
        var countryCounts = [];
        angular.forEach(countryData, function(country) {
          countryCounts.push(country[1]);
        });

        var dataset = {
          label: year,
          data: countryCounts,
          backgroundColor: getRandomColor()
        };

        $scope.chartConfig.data.datasets.push(dataset);
      });

      // Update the chart
      if ($scope.chart) {
        $scope.chart.destroy();
      }
      var ctx = document.getElementById('myChart').getContext('2d');
      $scope.chart = new Chart(ctx, $scope.chartConfig);
    };

    // Utility function to generate random colors
    function getRandomColor() {
      var letters = '0123456789ABCDEF';
      var color = '#';
      for (var i = 0; i < 6; i++) {
        color += letters[Math.floor(Math.random() * 16)];
      }
      return color;
    }

    // Initialize the chart
    $scope.updateChart();
  });
