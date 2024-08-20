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

#userInputContainer {
    margin-top: 20px;
}

#userAnswer {
    width: 100%; 
    margin-top: 10px;
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

.control {
    margin-top: 10px;
    margin-right: 10px;
}

.dropdown {
    margin-right: 10px;
}

.correct {
    color: #00883A;
}

.incorrect {
    color: #D71919;
}

.solution {
    text-decoration: underline;
}

#cnfStatus {
    margin-top: 10px;
    font-weight: bold;
}

#cnfButton.green {
    background-color: #90EE90;
}

#cnfButton.red {
    background-color: #D71919;
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
    <div id="cnf-status"></div>
    <div class="controls">
        <select id="modeSelect">
            <option value="">Modus auswählen</option>
            <option value="verify">Überprüfen</option>
            <option value="guided">Geleitete Übung</option>
        </select>
        <button id="startButton" disabled>Starten</button>
        <button id="resetButton" disabled>Zurücksetzen</button>
        <button id="solutionButton" disabled>Lösung zeigen</button>
        <button id="cnfButton">Ist in Chomsky Normalform?</button>
    </div>
    <div id="userInputContainer" style="margin-top: 20px;">
        <div id="question"></div>
        <input type="text" id="userAnswer"/>
        <button id="submitAnswerButton" disabled>Antworten</button>
        <div id="userFeedback" class="feedback"></div>
    </div>
</div>

<div class="container">
    <h2>Ausgabe:</h2>
    <div id="output" class="output"></div>
    <h2>Feedback:</h2>
    <div id="feedback" class="feedback"></div>
</div>

<script>
    document.addEventListener('DOMContentLoaded', function() {
        const modeSelect = document.getElementById('modeSelect');
        const startButton = document.getElementById('startButton');
        const resetButton = document.getElementById('resetButton');
        const solutionButton = document.getElementById('solutionButton');
        const submitAnswerButton = document.getElementById('submitAnswerButton');
        const userAnswerInput = document.getElementById('userAnswer');
        const questionDiv = document.getElementById('question');
        const cnfStatusDiv = document.getElementById('cnf-status');
        const userFeedbackDiv = document.getElementById('userFeedback');
        const feedbackDiv = document.getElementById('feedback');
        const cnfButton = document.getElementById('cnfButton');

        let currentStepIndex = 0;
        let steps = [];
        let V = [];
        let wordInput = '';
        let grammar = {};
        let mode = '';
        let userSteps = [];

        modeSelect.addEventListener('change', () => {
            mode = modeSelect.value;
            startButton.disabled = !mode;
            clearOutput();
        });

        startButton.addEventListener('click', processCYK);
        resetButton.addEventListener('click', resetProcess);
        solutionButton.addEventListener('click', displaySolution);
        submitAnswerButton.addEventListener('click', submitUserAnswer);
        cnfButton.addEventListener('click', checkCNF);

        function clearOutput() {
            document.getElementById('output').innerHTML = '';
            document.getElementById('feedback').innerHTML = '';
            cnfStatusDiv.textContent = '';
            userAnswerInput.value = '';
            questionDiv.textContent = '';
            userFeedbackDiv.innerHTML = '';
            cnfButton.classList.remove('green', 'red');
        }

        function processCYK() {
            const grammarInput = document.getElementById('grammar').value;
            wordInput = document.getElementById('word').value;

            grammar = parseGrammar(grammarInput);

            const result = runCYK(wordInput, grammar);
            V = result.V;
            steps = result.steps;
            currentStepIndex = 0;
            userSteps = [];

            clearOutput();

            resetButton.disabled = false;
            startButton.disabled = true;
            submitAnswerButton.disabled = true;
            solutionButton.disabled = false;

            if (mode === 'verify') {
                displayOutput(wordInput, V, result.calculated);
                displayFeedback(steps, wordInput, V);
            } else if (mode === 'guided') {
                stepProcess();
            }
        }

        function stepProcess() {
            if (currentStepIndex < steps.length) {
                const step = steps[currentStepIndex];
                if (mode === 'guided') {
                    questionDiv.innerHTML = `Was ist der Wert von V<sub>${step.substring}</sub>?`;
                    submitAnswerButton.disabled = false;
                    userAnswerInput.focus();
                }
            }
        }

        function submitUserAnswer() {
            const userAnswer = userAnswerInput.value.trim();
            const step = steps[currentStepIndex];
            const correctAnswerVariants = generateCorrectAnswerVariants(step.value);

            const userAnswerFormatted = userAnswer
                .split(',')
                .map(s => s.trim())
                .sort()
                .join(', ');

            const isCorrect = correctAnswerVariants.some(variant => 
                variant.includes(userAnswerFormatted) || 
                userAnswerFormatted.includes(variant)
            );

            if (isCorrect) {
                userFeedbackDiv.innerHTML = '<span class="correct">Deine Antwort ist richtig!</span>';
                displayStep(step, true, userAnswerFormatted);
                questionDiv.textContent = '';
                userAnswerInput.value = '';
                submitAnswerButton.disabled = true;
                currentStepIndex++;
                stepProcess();
            } else {
                userFeedbackDiv.innerHTML = '<span class="incorrect">Leider ist deine Antwort falsch.</span>';
                questionDiv.innerHTML = `Versuchen Sie es erneut: Was ist der Wert von V<sub>${step.substring}</sub>?`;
                userAnswerInput.focus();
            }
        }

        function displaySolution() {
            const step = steps[currentStepIndex];
            userFeedbackDiv.innerHTML = `<span class="solution">Die richtige Antwort ist: ${step.value}</span>`;
            displayStep(step, true, step.value);
            questionDiv.textContent = '';
            userAnswerInput.value = '';
            submitAnswerButton.disabled = true;
            currentStepIndex++;
            stepProcess();
        }

        function resetProcess() {
            clearOutput();

            currentStepIndex = 0;
            steps = [];
            V = [];
            userSteps = [];

            startButton.disabled = true;
            resetButton.disabled = true;
            modeSelect.value = '';
            solutionButton.disabled = true;
            submitAnswerButton.disabled = true;
        }

        function checkCNF() {
            const grammarInput = document.getElementById('grammar').value;
            grammar = parseGrammar(grammarInput);
            if (isCNF(grammar)) {
                cnfButton.classList.add('green');
                cnfButton.classList.remove('red');
            } else {
                cnfButton.classList.add('red');
                cnfButton.classList.remove('green');
            }
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

        function isCNF(grammar) {
            return Object.values(grammar).every(productions => 
                productions.every(production => 
                    (production.length === 1 && production.match(/[a-z]/)) || 
                    (production.length === 2 && production.match(/[A-Z]{2}/))
                )
            );
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
                                                value: [...V[i][l]].join(', '),
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

        function generateCorrectAnswerVariants(answer) {
            const answers = answer.split(',').map(s => s.trim());
            const variants = new Set();

            function permute(arr, m = []) {
                if (arr.length === 0) {
                    variants.add(m.join(', '));
                } else {
                    for (let i = 0; i < arr.length; i++) {
                        const curr = arr.slice();
                        const next = curr.splice(i, 1);
                        permute(curr.slice(), m.concat(next));
                    }
                }
            }

            permute(answers);
            return Array.from(variants);
        }

        function displayOutput(word, V, calculated) {
            const outputDiv = document.getElementById('output');
            outputDiv.innerHTML = '';
            calculated.forEach(substring => {
                const i = word.indexOf(substring);
                const l = substring.length - 1;
                const result = `V<sub>${substring}</sub> = {${[...V[i][l]].join(', ')}}`;
                const p = document.createElement('p');
                p.innerHTML = result;
                outputDiv.appendChild(p);
            });
        }

        function displayFeedback(steps, word, V) {
            const feedbackDiv = document.getElementById('feedback');
            const detailedSteps = steps.map(step => {
                if (step.components) {
                    return `V<sub>${step.substring}</sub> = { X | X -> (${step.components.join(' ∪ ')}) = {${step.value}} }`;
                } else {
                    return `V<sub>${step.substring}</sub> = { ${step.value} } da ${step.rule}`;
                }
            });

            feedbackDiv.innerHTML = `Ausführliche Rechenweg:<br>${detailedSteps.join('<br>')}`;

            const finalResult = V[0][word.length - 1].has('S') ?
                `Das Wort "${word}" ist in der Sprache, weil S ∈ V<sub>${word}</sub>` :
                `Das Wort "${word}" ist nicht in der Sprache, weil S ∉ V<sub>${word}</sub>`;

            const conclusion = document.createElement('p');
            conclusion.innerHTML = finalResult;
            feedbackDiv.appendChild(conclusion);
        }

        function displayStep(step, isCorrect, userAnswer) {
            const outputDiv = document.getElementById('output');
            const feedbackDiv = document.getElementById('feedback');

            if (isCorrect) {
                const result = `V<sub>${step.substring}</sub> = {${step.value}}`;
                const p = document.createElement('p');
                p.innerHTML = result;
                outputDiv.appendChild(p);

                const feedback = step.components ?
                    `V<sub>${step.substring}</sub> = ({ X | X -> ${step.components.join(' ∪ ')}) = {${step.value}} }` :
                    `V<sub>${step.substring}</sub> = { ${step.value} } da ${step.rule}`;
                const f = document.createElement('p');
                f.innerHTML = `${feedback} (Ihre Eingabe: <span class="correct">${userAnswer}</span>)`;
                feedbackDiv.appendChild(f);
            }
        }
    });
</script>

</p>
</div>
