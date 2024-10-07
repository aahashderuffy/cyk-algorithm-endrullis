---
layout: default
permalink: /calculate/
---

<div style="padding: 20px;">
<h1>Cocke-Younger-Kasami-Algorithmus - Berechnen</h1>
<p>

<style>
.container {
    width: 30%; 
    float: left;
}

.output-container{
    width: 50%; 
    float: right;    
}

h2 {
    text-decoration: underline;
}

textarea {
    width: 100%;
    height: 100px;
    margin-bottom: 10px;
    resize: none;
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

#output {
    margin: 20px;
}

#feedback {
    margin: 20px;
}

#userInputContainer {
    margin-top: 20px;
}

.controls {
    margin-top: 10px;
    margin-right: 10px;
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
    </div>
    <div id="userInputContainer" style="margin-top: 20px;">
        <div id="question"></div>
        <input type="text" id="userAnswer"/>
        <button id="submitAnswerButton" disabled>Antworten</button>
        <div id="userFeedback" class="feedback"></div>
    </div>
</div>

<div class="output-container">
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

        function clearOutput() {
            document.getElementById('output').innerHTML = '';
            document.getElementById('feedback').innerHTML = '';
            cnfStatusDiv.textContent = '';
            userAnswerInput.value = '';
            questionDiv.textContent = '';
            userFeedbackDiv.innerHTML = '';
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
                if (isCNF(grammar)) {
                    userFeedbackDiv.innerHTML = '<span class="correct">Ihre eingegebene Grammatik liegt in Chomsky-Normalform.</span>';
                } else {
                    userFeedbackDiv.innerHTML = '<span class="incorrect">Ihre eingegebene Grammatik liegt nicht in Chomsky-Normalform.</span>';
                }
            } else if (mode === 'guided') {
                if (isCNF(grammar)) {
                    userFeedbackDiv.innerHTML = '<span class="correct">Ihre eingegebene Grammatik liegt in Chomsky-Normalform.</span>';
                } else {
                    userFeedbackDiv.innerHTML = '<span class="incorrect">Ihre eingegebene Grammatik liegt nicht in Chomsky-Normalform.</span>';
                }
                askNewQuestion();
            }
        }

        function askNewQuestion() {
            if (currentStepIndex < steps.length) {
                const step = steps[currentStepIndex];
                questionDiv.innerHTML = `Was ist der Wert von V<sub>${step.substring}</sub>?`;
                submitAnswerButton.disabled = false;
                userAnswerInput.focus();
            } else {
                questionDiv.innerHTML = 'Geschafft! Alle Schritte wurden berechnet.';
                submitAnswerButton.disabled = true;
                displayOutput(wordInput, V, new Set(steps.map(s => s.substring)));
            }
        }

        function submitUserAnswer() {
            const userAnswer = userAnswerInput.value.trim();

            if (userAnswer === '') {
                userFeedbackDiv.innerHTML = '<span class="incorrect">Ungültige Eingabe. Ihre Antwort darf nicht leer sein. Wenn Sie Schwierigkeiten haben, haben Sie die Möglichkeit, sich die Lösung sofort anzeigen zu lassen, indem Sie auf die Schaltfläche „Lösung anzeigen“ klicken.</span>';
                userAnswerInput.focus();
                return;
            }

            const step = steps[currentStepIndex];
            const correctAnswers = generateCorrectAnswerVariants(step.value);

            const correctAnswersSet = new Set(correctAnswers);
            const userAnswerFormatted = userAnswer
                .split(',')
                .map(s => s.trim())
                .sort()
                .join(', ');

            const isCorrect = correctAnswersSet.has(userAnswerFormatted);

            if (isCorrect) {
                userFeedbackDiv.innerHTML = '<span class="correct">Deine Antwort ist richtig!</span>';
                displayStep(step, true, userAnswerFormatted);
                questionDiv.textContent = '';
                userAnswerInput.value = '';
                submitAnswerButton.disabled = true;
                currentStepIndex++;

                displayOutput(wordInput, V, new Set(steps.slice(0, currentStepIndex).map(s => s.substring)));

                askNewQuestion();
            } else {
                userFeedbackDiv.innerHTML = '<span class="incorrect">Leider ist deine Antwort falsch.</span> Tipp: Überprüfen Sie die Produktionsregeln und die bisherigen Berechnungen.';
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
            askNewQuestion();
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
            for (let j = 1; j < word.length; j++) {
                for (let i = 0; i < word.length - j; i++) {
                    for (let k = 0; k < j; k++) {
                        for (const [left, right] of Object.entries(grammar)) {
                            right.forEach(production => {
                                if (production.length === 2) {
                                    const [B, C] = production;
                                    if (V[i][k].has(B) && V[i + k + 1][j - k - 1].has(C)) {
                                        V[i][j].add(left);
                                        const substring = word.slice(i, i + j + 1);
                                        const part1 = word.slice(i, i + k + 1);
                                        const part2 = word.slice(i + k + 1, i + j + 1);
                                        if (!calculated.has(substring)) {
                                            steps.push({
                                                substring: substring,
                                                value: [...V[i][j]].join(', '),
                                                rule: `${left} -> ${production}`,
                                                combination: `V<sub>${part1}</sub>V<sub>${part2}</sub> = {${B}${C}}`
                                            });
                                            calculated.add(substring);
                                        } else {
                                            const stepIndex = steps.findIndex(step => step.substring === substring);
                                            if (stepIndex !== -1) {
                                                steps[stepIndex].value = [...new Set(steps[stepIndex].value.split(', ').concat(left))].join(', ');
                                            }
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
                const j = substring.length - 1;
                const result = `V<sub>${substring}</sub> = {${[...V[i][j]].join(', ')}}`;
                const p = document.createElement('p');
                p.innerHTML = result;
                outputDiv.appendChild(p);
            });
        }

        function displayFeedback(steps, word, V) {
            const feedbackDiv = document.getElementById('feedback');
            const detailedSteps = [];

            steps.forEach(step => {
                let combination = step.combination;
                if (step.combination){
                    let output = `V<sub>${step.substring}</sub> = { X | X -> ${combination}} = {${step.value}}`;
                    detailedSteps.push(output);
                } else {
                    let output = `V<sub>${step.substring}</sub> = { ${step.value} } da ${step.rule}`;
                    detailedSteps.push(output);
                }
            });

            feedbackDiv.innerHTML = `Ausführlicher Rechenweg:<br>${detailedSteps.join('<br>')}`;

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
            
            const existingOutput = Array.from(outputDiv.children).map(p => p.innerHTML);
            const stepResult = `V<sub>${step.substring}</sub> = {${step.value}}`;
            
            if (!existingOutput.includes(stepResult)) {
                const p = document.createElement('p');
                p.innerHTML = stepResult;
                outputDiv.appendChild(p);
            }

            let feedback;
            if (step.combination) {
                feedback = `V<sub>${step.substring}</sub> = { X | X -> ${step.combination}} = {${step.value}}`;
            } else {
                feedback = `V<sub>${step.substring}</sub> = { ${step.value} } da ${step.rule}`;
            }
            const existingFeedback = Array.from(feedbackDiv.children).map(f => f.innerHTML);
            if (!existingFeedback.includes(feedback)) {
                const f = document.createElement('p');
                f.innerHTML = `${feedback} (Ihre Eingabe: <span class="correct">${userAnswer}</span>)`;
                feedbackDiv.appendChild(f);
            }
        }

    });
</script>

</p>
</div>
