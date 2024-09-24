<script lang="ts">
	import party from "party-js";

	interface Question {
		question: string;
		negative: boolean;
		correctAnswer:
			| "must"
			| "musn't"
			| "have to"
			| "don't have to"
			| "has to";
	}

	const POSSIBLE_ANSWERS = [
		"must",
		"musn't",
		"have to",
		"don't have to",
		"has to",
	];

	let quizState = "nothing";
	let started = false;
	let currentQuestion: Question = {
		question: "",
		negative: false,
		correctAnswer: "have to",
	};
	let generatedChoices = ["", ""];
	let score = 0;

	let firstBtn: HTMLButtonElement;
	let secondBtn: HTMLButtonElement;

	let questions: Question[] = [
		{
			question: "You _ arrive on time for the interview.",
			negative: false,
			correctAnswer: "must",
		},
		{
			question: "Students _ complete all assignments to pass the course.",
			negative: false,
			correctAnswer: "must",
		},
		{
			question: "You _ touch that wire; it's dangerous.",
			negative: true,
			correctAnswer: "musn't",
		},
		{
			question: "Visitors _ feed the animals at the zoo.",
			negative: true,
			correctAnswer: "musn't",
		},
		{
			question: "I _ finish this report by tomorrow.",
			negative: false,
			correctAnswer: "have to",
		},
		{
			question: "She _ wake up early to catch her flight.",
			negative: false,
			correctAnswer: "has to",
		},
		{
			question: "You _ attend the meeting if you're busy.",
			negative: true,
			correctAnswer: "don't have to",
		},
		{
			question: "We _ pay for parking on Sundays.",
			negative: true,
			correctAnswer: "don't have to",
		},
	];

	function shuffle(array: Array<any>) {
		let currentIndex = array.length;

		// While there remain elements to shuffle...
		while (currentIndex != 0) {
			// Pick a remaining element...
			let randomIndex = Math.floor(Math.random() * currentIndex);
			currentIndex--;

			// And swap it with the current element.
			[array[currentIndex], array[randomIndex]] = [
				array[randomIndex],
				array[currentIndex],
			];
		}
	}

	shuffle(questions);

	function getRandomIndex(array: Array<any>) {
		return Math.floor(Math.random() * array.length);
	}

	function nextQuestion() {
		if (questions.length === 0) {
			quizState = "ended";
		}

		currentQuestion = questions.pop() ?? {
			correctAnswer: "have to",
			negative: false,
			question: "",
		};

		let correctIndex = Math.round(Math.random());
		generatedChoices[correctIndex] = currentQuestion.correctAnswer;

		let wrongAnswer;

		// Keep regenerating answers just in case the user got lucky and got both choices as the correct answer
		do {
			wrongAnswer = POSSIBLE_ANSWERS[getRandomIndex(POSSIBLE_ANSWERS)];
		} while (wrongAnswer === currentQuestion.correctAnswer);

		if (correctIndex === 0) {
			generatedChoices[1] = wrongAnswer;
		} else {
			generatedChoices[0] = wrongAnswer;
		}
	}

	function start() {
		nextQuestion();

		quizState = "started";
	}

	function onFirstButtonClicked() {
		if (generatedChoices[0] == currentQuestion.correctAnswer) {
			party.confetti(firstBtn, {
				count: party.variation.range(20, 40),
			});
			score++;
		} else {
		}

		nextQuestion();
	}

	function onSecondButtonClicked() {
		if (generatedChoices[1] == currentQuestion.correctAnswer) {
			party.confetti(secondBtn, {
				count: party.variation.range(20, 40),
			});
			score++;
		} else {
		}

		nextQuestion();
	}
</script>

<main
	class="max-w-screen-md flex items-center justify-center flex-col gap-2 text-center"
>
	{#if quizState === "started"}
		<h1>{currentQuestion.question}</h1>
		<div class="flex gap-2">
			<button
				bind:this={firstBtn}
				on:click={onFirstButtonClicked}
				class="rounded-3xl bg-gray-100 py-2 px-4 text-black"
				>{generatedChoices[0]}</button
			>
			<button
				bind:this={secondBtn}
				on:click={onSecondButtonClicked}
				class="rounded-3xl bg-gray-100 py-2 px-4 text-black"
				>{generatedChoices[1]}</button
			>
		</div>
	{:else if quizState === "ended"}
		<h1>Result!</h1>
		<p>You got... {score} score!</p>
		<a
			href="/eng-have-to/"
			class="rounded-3xl bg-gray-100 py-2 px-4 text-black"
		>
			Back to home
		</a>
	{:else}
		<h1>Quiz time!</h1>
		<noscript>This webpage requires JavaScript to function.</noscript>
		<button
			class="rounded-3xl bg-gray-100 py-2 px-4 text-black"
			on:click={start}>Next</button
		>
	{/if}
</main>
