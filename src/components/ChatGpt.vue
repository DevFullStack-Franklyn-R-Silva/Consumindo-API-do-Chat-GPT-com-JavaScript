<template>
    <body class="content">
        <textarea
            id="result"
            rows="10"
            disabled
            placeholder="Resposta da IA"
            style="height: 70%"
        ></textarea>
        <textarea
            id="inputQuestion"
            rows="10"
            placeholder="Pergunte algo..."
            style="height: 30%"
        ></textarea>
    </body>
</template>

<script>
export default {
    name: "ChatGpt",
    props: {},
    mounted() {
        const inputQuestion = document.getElementById("inputQuestion");
        // const result = document.getElementById("result");

        inputQuestion.addEventListener("keypress", (e) => {
            if (inputQuestion.value && e.key === "Enter") this.SendQuestion();
        });
    },
    methods: {
        SendQuestion() {
            const inputQuestion = document.getElementById("inputQuestion");
            const result = document.getElementById("result");
            const OPENAI_API_KEY =
                "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";

            var sQuestion = inputQuestion.value;

            fetch("https://api.openai.com/v1/completions", {
                method: "POST",
                headers: {
                    Accept: "application/json",
                    "Content-Type": "application/json",
                    Authorization: "Bearer " + OPENAI_API_KEY,
                },
                body: JSON.stringify({
                    model: "text-davinci-003",
                    prompt: sQuestion,
                    max_tokens: 2048,
                    temperature: 0.5,
                }),
            })
                .then((response) => response.json())
                .then((json) => {
                    if (result.value) result.value += "\n";

                    if (json.error?.message) {
                        result.value += `Error: ${json.error.message}`;
                    } else if (json.choices?.[0].text) {
                        var text = json.choices[0].text || "Sem resposta";
                        result.value += "Chat GPT: " + text;
                    }

                    result.scrollTop = result.scrollHeight;
                })
                .catch((error) => console.error("Error:", error))
                .finally(() => {
                    inputQuestion.value = "";
                    inputQuestion.disabled = false;
                    inputQuestion.focus();
                });

            if (result.value) result.value += "\n\n\n";

            result.value += `Eu: ${sQuestion}`;
            inputQuestion.value = "Carregando...";
            inputQuestion.disabled = true;

            result.scrollTop = result.scrollHeight;
        },
    },
};
</script>

<style scoped>
* {
    margin: 0;
    padding: 0;
    box-sizing: border-box;
}

body {
    background-color: #1d1e23;
    display: flex;
    justify-content: center;
    width: 100vw;
    height: 100vh;
}

.content {
    display: flex;
    flex-direction: column;
    justify-content: space-around;
}

#result {
    margin: 50px;
    resize: none;
    border: none;
    padding: 8px;
    background-color: #3c3d46;
    color: white;
    outline: none;
    font-size: 24px;
    text-align: justify;
    border-radius: 10px;
}
#inputQuestion {
    margin: 50px;
    resize: none;
    border: none;
    padding: 8px;
    background-color: #3c3d46;
    color: white;
    outline: none;
    font-size: 24px;
    text-align: justify;
    border-radius: 10px;
}

#inputQuestion:focus {
    border: 1px solid #8257e5;
}
</style>
