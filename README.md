import React, { useState, useEffect, useRef } from "react";

const facts = [
  "Octopuses have three hearts.",
  "Bananas are technically berries.",
  "Honey never spoils.",
  "Sharks existed before trees.",
  "A day on Venus is longer than a year on Venus.",
  "The Eiffel Tower grows slightly in summer heat.",
  "There are more stars than grains of sand on Earth.",
  "Wombats have cube-shaped poop."
];

const trivia = [
  { q: "What is the capital of Japan?", options: ["Seoul", "Tokyo", "Kyoto", "Beijing"], a: "Tokyo" },
  { q: "Which planet is known as the Red Planet?", options: ["Mars", "Venus", "Jupiter", "Saturn"], a: "Mars" },
  { q: "How many continents are there?", options: ["5", "6", "7", "8"], a: "7" },
  { q: "Which ocean is the largest?", options: ["Atlantic", "Pacific", "Indian", "Arctic"], a: "Pacific" },
  { q: "Who painted the Mona Lisa?", options: ["Van Gogh", "Da Vinci", "Picasso", "Rembrandt"], a: "Da Vinci" },
  { q: "What gas do plants absorb?", options: ["Oxygen", "Carbon Dioxide", "Nitrogen", "Hydrogen"], a: "Carbon Dioxide" },
  { q: "What is the tallest mountain in the world?", options: ["K2", "Everest", "Denali", "Kilimanjaro"], a: "Everest" },
  { q: "Which animal is the fastest on land?", options: ["Lion", "Cheetah", "Horse", "Tiger"], a: "Cheetah" }
];

const typingTexts = [
  "During a long flight across the ocean, the cabin lights dim and the quiet hum of the engines fills the air. Passengers read books, watch movies, or stare out the window at the endless sky. Far below, clouds drift slowly like floating islands. For someone who enjoys learning and building things, a flight can become the perfect time to practice typing, play games, solve puzzles, or write code for a new project.",
  "The quick brown fox jumps over the lazy dog.",
  "Typing fast takes practice and focus.",
  "Airplane flights are a great time to play games.",
  "Coding small projects is a fun way to learn programming.",
  "Stay curious and keep building cool things."
];

function GuessNumber() {
  const [target] = useState(() => Math.floor(Math.random() * 100) + 1);
  const [guess, setGuess] = useState("");
  const [message, setMessage] = useState("Guess a number 1–100");

  const check = () => {
    const g = Number(guess);
    if (g === target) setMessage("Correct! 🎉");
    else if (g > target) setMessage("Too high");
    else setMessage("Too low");
  };

  return (
    <div className="p-4">
      <h2 className="text-xl font-bold">Number Guess</h2>
      <p>{message}</p>
      <input className="border p-2 mr-2" value={guess} onChange={(e) => setGuess(e.target.value)} placeholder="Enter guess" />
      <button className="bg-black text-white px-3 py-2 rounded" onClick={check}>Guess</button>
    </div>
  );
}

function Trivia() {
  const [index, setIndex] = useState(0);
  const [result, setResult] = useState("");

  const q = trivia[index];

  const answer = (opt) => {
    if (opt === q.a) setResult("Correct! ✅");
    else setResult("Wrong ❌");
  };

  const next = () => {
    setIndex((i) => (i + 1) % trivia.length);
    setResult("");
  };

  return (
    <div className="p-4">
      <h2 className="text-xl font-bold">Trivia</h2>
      <p className="mb-2">{q.q}</p>
      <div className="grid grid-cols-2 gap-2">
        {q.options.map((o) => (
          <button key={o} onClick={() => answer(o)} className="border p-2 rounded hover:bg-gray-100">{o}</button>
        ))}
      </div>
      <p className="mt-2">{result}</p>
      <button className="mt-2 underline" onClick={next}>Next Question</button>
    </div>
  );
}

function RandomFact() {
  const [fact, setFact] = useState("");

  const generate = () => {
    const f = facts[Math.floor(Math.random() * facts.length)];
    setFact(f);
  };

  return (
    <div className="p-4">
      <h2 className="text-xl font-bold">Random Fact</h2>
      <button className="bg-black text-white px-3 py-2 rounded" onClick={generate}>New Fact</button>
      <p className="mt-2">{fact}</p>
    </div>
  );
}

function TypingSpeed() {
  const [text] = useState(() => typingTexts[Math.floor(Math.random() * typingTexts.length)]);
  const [input, setInput] = useState("");
  const [start, setStart] = useState(null);
  const [wpm, setWpm] = useState(0);
  const [bestWpm, setBestWpm] = useState(() => {
    const saved = localStorage.getItem("bestWpm");
    return saved ? Number(saved) : 0;
  });

  const handleChange = (e) => {
    const value = e.target.value;

    if (!start) setStart(Date.now());

    setInput(value);

    if (value === text) {
      const time = (Date.now() - start) / 1000;
      const words = text.split(" ").length;
      const speed = Math.round((words / time) * 60);
      setWpm(speed);
      if (speed > bestWpm) {
        setBestWpm(speed);
        localStorage.setItem("bestWpm", speed);
      }
    }
  };

  return (
    <div className="p-4">
      <h2 className="text-xl font-bold mb-2">Typing Speed Test</h2>
      <p className="mb-3 border p-2 bg-gray-50">{text}</p>
      <textarea
        className="border w-full p-2"
        rows={3}
        value={input}
        onChange={handleChange}
        placeholder="Start typing..."
      />
      <div className="mt-2">
      <p className="font-bold">⚡ WPM: {wpm}</p>
      <p className="text-sm">🏆 Best WPM: {bestWpm}</p>
      </div>
    </div>
  );
}

function DrawingPad() {
  const canvasRef = useRef(null);
  const drawing = useRef(false);

  const start = (e) => {
    drawing.current = true;
    draw(e);
  };

  const end = () => {
    drawing.current = false;
    const ctx = canvasRef.current.getContext("2d");
    ctx.beginPath();
  };

  const draw = (e) => {
    if (!drawing.current) return;
    const canvas = canvasRef.current;
    const ctx = canvas.getContext("2d");

    const rect = canvas.getBoundingClientRect();
    const x = e.clientX - rect.left;
    const y = e.clientY - rect.top;

    ctx.lineWidth = 3;
    ctx.lineCap = "round";
    ctx.strokeStyle = "black";

    ctx.lineTo(x, y);
    ctx.stroke();
    ctx.beginPath();
    ctx.moveTo(x, y);
  };

  const clear = () => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext("2d");
    ctx.clearRect(0, 0, canvas.width, canvas.height);
  };

  return (
    <div className="p-4 text-center">
      <h2 className="text-xl font-bold mb-2">Drawing Pad</h2>
      <canvas
        ref={canvasRef}
        width={400}
        height={300}
        className="border mx-auto"
        onMouseDown={start}
        onMouseUp={end}
        onMouseMove={draw}
        onMouseLeave={end}
      />
      <div className="mt-2">
        <button className="border px-3 py-1 rounded" onClick={clear}>Clear</button>
      </div>
    </div>
  );
}

function SnakeGame() {
  const canvasRef = useRef(null);
  const grid = 20;
  const [snake, setSnake] = useState([{ x: 8, y: 8 }]);
  const [food, setFood] = useState({ x: 5, y: 5 });
  const [dir, setDir] = useState({ x: 1, y: 0 });

  useEffect(() => {
    const handleKey = (e) => {
      if (e.key === "ArrowUp") setDir({ x: 0, y: -1 });
      if (e.key === "ArrowDown") setDir({ x: 0, y: 1 });
      if (e.key === "ArrowLeft") setDir({ x: -1, y: 0 });
      if (e.key === "ArrowRight") setDir({ x: 1, y: 0 });
    };

    window.addEventListener("keydown", handleKey);
    return () => window.removeEventListener("keydown", handleKey);
  }, []);

  useEffect(() => {
    const interval = setInterval(() => {
      setSnake((prev) => {
        const head = { x: (prev[0].x + dir.x + grid) % grid, y: (prev[0].y + dir.y + grid) % grid };
        const newSnake = [head, ...prev];

        if (head.x === food.x && head.y === food.y) {
          setFood({ x: Math.floor(Math.random() * grid), y: Math.floor(Math.random() * grid) });
        } else {
          newSnake.pop();
        }

        return newSnake;
      });
    }, 150);

    return () => clearInterval(interval);
  }, [dir, food]);

  useEffect(() => {
    const canvas = canvasRef.current;
    const ctx = canvas.getContext("2d");

    ctx.clearRect(0, 0, canvas.width, canvas.height);

    ctx.fillStyle = "green";
    snake.forEach((s) => ctx.fillRect(s.x * 20, s.y * 20, 18, 18));

    ctx.fillStyle = "red";
    ctx.fillRect(food.x * 20, food.y * 20, 18, 18);
  }, [snake, food]);

  return (
    <div className="p-4 text-center">
      <h2 className="text-xl font-bold mb-2">Snake</h2>
      <p className="text-sm mb-2">Use arrow keys</p>
      <canvas ref={canvasRef} width={400} height={400} className="border mx-auto" />
    </div>
  );
}

export default function FlightFunSite() {
  const [tab, setTab] = useState("game");

  return (
    <div className="min-h-screen bg-gray-50 text-center p-6">
      <h1 className="text-3xl font-bold mb-4">✈️ Flight Fun</h1>

      <div className="flex flex-wrap justify-center gap-3 mb-6">
        <button className="border px-4 py-2 rounded" onClick={() => setTab("game")}>Game</button>
        <button className="border px-4 py-2 rounded" onClick={() => setTab("snake")}>Snake</button>
        <button className="border px-4 py-2 rounded" onClick={() => setTab("draw")}>Draw</button>
        <button className="border px-4 py-2 rounded" onClick={() => setTab("typing")}>Typing</button>
        <button className="border px-4 py-2 rounded" onClick={() => setTab("trivia")}>Trivia</button>
        <button className="border px-4 py-2 rounded" onClick={() => setTab("facts")}>Facts</button>
      </div>

      <div className="max-w-xl mx-auto bg-white rounded-2xl shadow p-4">
        {tab === "game" && <GuessNumber />}
        {tab === "snake" && <SnakeGame />}
        {tab === "draw" && <DrawingPad />}
        {tab === "typing" && <TypingSpeed />}
        {tab === "trivia" && <Trivia />}
        {tab === "facts" && <RandomFact />}
      </div>

      <p className="mt-6 text-sm text-gray-500">Works offline once loaded.</p>
    </div>
  );
}
