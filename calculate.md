---
layout: default
permalink: /calculate/
---

<div style="padding: 20px;">
<h1>Cocke-Younger-Kasami-Algorithmus - Berechnen</h1>
<p>

<style>
.container {
    width: 50%; 
}

h2 {
    text-decoration: underline;
}
    
textarea {
    width: 100%;
    height: 100px;
    margin-bottom: 10px;
}

.output {
    border: 1px solid #232323;
    overflow-y: auto;
    border-width: thick;
    padding: 10px;
    height: 300px;
}

.feedback {
    border: 1px solid #00883A;
    overflow-y: auto;
    border-width: thick;
    padding: 10px;
    height: 300px;
}
</style>

<div class="container">
    <h2>Eingabe:</h2>
    <label for="grammar">Grammatik:</label>
    <textarea id="grammar">
S -> AB
A -> BB | a
B -> AB | b
    </textarea>
    <label for="word">Wort:</label>
    <textarea id="word">abbb</textarea>
    <button id="processButton">Überprüfen</button>
</div>

<div class="container">
    <h2>Ausgabe:</h2>
    <div id="output" class="output"></div>
    <h2>Feedback:</h2>
    <div id="feedback" class="feedback"></div>
</div>

<script>
    document.addEventListener('DOMContentLoaded', function() {
        const processButton = document.getElementById('processButton');
        processButton.addEventListener('click', processCYK);

        function processCYK() {
            const grammarInput = document.getElementById('grammar').value;
            const wordInput = document.getElementById('word').value;
            const outputDiv = document.getElementById('output');
            const feedbackDiv = document.getElementById('feedback');

            const grammar = parseGrammar(grammarInput);
            const { V, steps, calculated } = runCYK(wordInput, grammar);

            displayOutput(outputDiv, wordInput, V, calculated);
            displayFeedback(feedbackDiv, steps, wordInput, V);
        }

        function parseGrammar(input) {
            return input.trim().split('\n').reduce((grammar, rule) => {
                const [left, right] = rule.split('->').map(part => part.trim());
                right.split('|').forEach(production => {
                    if (!grammar[left]) { 
                        grammar[left] = []; 
                    }
                    grammar[left].push(production.trim());
                });
                return grammar;
            }, {});
        }

        function runCYK(word, grammar) {
            const n = word.length;
            const V = Array.from({ length: n }, () => Array.from({ length: n }, () => new Set()));
            const steps = [];
            const calculated = new Set();

            initializeTable(V, word, grammar, steps, calculated);
            fillTable(V, word, grammar, steps, calculated);

            return { V, steps, calculated };
        }

        function initializeTable(V, word, grammar, steps, calculated) {
            for (let i = 0; i < word.length; i++) {
                for (const [left, right] of Object.entries(grammar)) {
                    if (right.includes(word[i])) {
                        V[i][0].add(left);
                        if (!calculated.has(word[i])) {
                            steps.push({ substring: word[i], value: left, rule: `${left} -> ${word[i]}` });
                            calculated.add(word[i]);
                        }
                    }
                }
            }
        }

        function fillTable(V, word, grammar, steps, calculated) {
            for (let l = 1; l < word.length; l++) {
                for (let i = 0; i < word.length - l; i++) {
                    for (let k = 0; k < l; k++) {
                        for (const [left, right] of Object.entries(grammar)) {
                            right.forEach(production => {
                                if (production.length === 2) {
                                    const [B, C] = production;
                                    if (V[i][k].has(B) && V[i + k + 1][l - k - 1].has(C)) {
                                        V[i][l].add(left);
                                        const substring = word.slice(i, i + l + 1);
                                        if (!calculated.has(substring)) {
                                            steps.push({
                                                substring: substring,
                                                value: left,
                                                rule: `${left} -> ${production}`,
                                                components: [
                                                    `V<sub>${word.slice(i, i + k + 1)}</sub> = {${B}}`,
                                                    `V<sub>${word.slice(i + k + 1, i + l + 1)}</sub> = {${C}}`
                                                ]
                                            });
                                            calculated.add(substring);
                                        }
                                    }
                                }
                            });
                        }
                    }
                }
            }
        }

        function displayOutput(outputDiv, word, V, calculated) {
            outputDiv.innerHTML = '';
            calculated.forEach(substring => {
                const i = word.indexOf(substring);
                const l = substring.length - 1;
                const result = `Für ${substring}: V = {${[...V[i][l]].join(',')}}`;
                const p = document.createElement('p');
                p.innerHTML = result;
                outputDiv.appendChild(p);
            });
        }

        function displayFeedback(feedbackDiv, steps, word, V) {
            const detailedSteps = steps.map(step => {
                if (step.components) {
                    return `V<sub>${step.substring}</sub> = { X | X -> ${step.components.join(' &cup; ')} = {${step.value}} }`;
                } else {
                    return `V<sub>${step.substring}</sub> = { ${step.value} } da ${step.rule}`;
                }
            });

            feedbackDiv.innerHTML = `Ausführliche Rechenweg:<br>${detailedSteps.join('<br>')}`;

            const finalResult = V[0][word.length - 1].has('S') ?
                `Das Wort "${word}" ist in der Sprache, weil S &isin; V<sub>${word}</sub>` :
                `Das Wort "${word}" ist nicht in der Sprache, weil S &notin; V<sub>${word}</sub>`;

            const conclusion = document.createElement('p');
            conclusion.innerHTML = finalResult;
            feedbackDiv.appendChild(conclusion);
        }
    });
</script>

</p>
</div>
