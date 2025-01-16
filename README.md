const fs = require('fs');


function decodeValue(base, value) {
    return parseInt(value, base);
}


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


function findConstantTerm(filename) {
    
    const data = JSON.parse(fs.readFileSync(filename, 'utf-8'));

    const { n, k } = data.keys; 
    const points = [];

    
    for (let i = 1; i <= n; i++) {
        const base = parseInt(data[i].base, 10);
        const value = data[i].value;
        const x = i; 
        const y = decodeValue(base, value); 
        points.push([x, y]);
    }

    
    const requiredPoints = points.slice(0, k);
    const secret = lagrangeInterpolation(requiredPoints);

    return secret;
}


const testcase1 = 'testcase1.json';
const testcase2 = 'testcase2.json';

try {
    const secret1 = findConstantTerm(testcase1);
    const secret2 = findConstantTerm(testcase2);

    console.log(`Secret for Test Case 1: ${secret1}`);
    console.log(`Secret for Test Case 2: ${secret2}`);
} catch (error) {
    console.error('An error occurred:', error.message);
}
