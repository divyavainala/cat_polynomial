
// Import the required modules
const fs = require('fs');

// Function to decode values based on the base
function decodeValue(base, value) {
    return parseInt(value, base);
}

// Function to compute the Lagrange Interpolation
function lagrangeInterpolation(points) {
    let c = 0;

    for (let i = 0; i < points.length; i++) {
        let xi = points[i][0];
        let yi = points[i][1];

        let li = 1;
        for (let j = 0; j < points.length; j++) {
            if (i !== j) {
                let xj = points[j][0];
                li *= -xj / (xi - xj);
            }
        }
        c += yi * li;
    }

    return Math.round(c);
}

// Main function to parse input, decode values, and find the secret
function findConstantTerm(filename) {
    // Read and parse the JSON file
    const data = JSON.parse(fs.readFileSync(filename, 'utf-8'));

    const { n, k } = data.keys; // Extract n and k from the JSON
    const points = [];

    // Decode the points from the JSON data
    for (let i = 1; i <= n; i++) {
        const base = parseInt(data[i].base, 10);
        const value = data[i].value;
        const x = i; // Use the key index as x
        const y = decodeValue(base, value); // Decode y using the base
        points.push([x, y]);
    }

    // Use the first k points to compute the polynomial's constant term
    const requiredPoints = points.slice(0, k);
    const secret = lagrangeInterpolation(requiredPoints);

    return secret;
}

// Test cases
const testcase1 = 'testcase1.json';
const testcase2 = 'testcase2.json';

try {
    const secret1 = findConstantTerm(testcase1);
    const secret2 = findConstantTerm(testcase2);

    console.log(Secret for Test Case 1: ${secret1});
    console.log(Secret for Test Case 2: ${secret2});
} catch (error) {
    console.error('An error occurred:', error.message);
}
