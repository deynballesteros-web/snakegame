import { useEffect, useRef, useState } from "react"; import { Card, CardContent } from "@/components/ui/card"; import { Button } from "@/components/ui/button"; import { motion, AnimatePresence } from "framer-motion";

const GRID_SIZE = 15; const INITIAL_SNAKE = [{ x: 7, y: 7 }]; const INITIAL_FOOD = { x: 3, y: 5 };

export default function ValentineSnakeGame() { const [snake, setSnake] = useState(INITIAL_SNAKE); const [food, setFood] = useState(INITIAL_FOOD); const [direction, setDirection] = useState({ x: 1, y: 0 }); const [eaten, setEaten] = useState(0); const [showPopup, setShowPopup] = useState(false); const [celebrate, setCelebrate] = useState(false); const audioRef = useRef(null);

const moveRef = useRef(direction); moveRef.current = direction;

useEffect(() => { const handleKey = (e) => { if (e.key === "ArrowUp") setDirection({ x: 0, y: -1 }); if (e.key === "ArrowDown") setDirection({ x: 0, y: 1 }); if (e.key === "ArrowLeft") setDirection({ x: -1, y: 0 }); if (e.key === "ArrowRight") setDirection({ x: 1, y: 0 }); }; window.addEventListener("keydown", handleKey); return () => window.removeEventListener("keydown", handleKey); }, []);

useEffect(() => { if (showPopup) return;

const interval = setInterval(() => {
  setSnake((prev) => {
    const head = prev[0];
    const newHead = {
      x: (head.x + moveRef.current.x + GRID_SIZE) % GRID_SIZE,
      y: (head.y + moveRef.current.y + GRID_SIZE) % GRID_SIZE,
    };

    let newSnake = [newHead, ...prev];

    if (newHead.x === food.x && newHead.y === food.y) {
      setEaten((e) => e + 1);
      setFood({
        x: Math.floor(Math.random() * GRID_SIZE),
        y: Math.floor(Math.random() * GRID_SIZE),
      });
    } else {
      newSnake.pop();
    }

    return newSnake;
  });
}, 200);

return () => clearInterval(interval);

}, [food, showPopup]);

useEffect(() => { if (eaten === 5) { setShowPopup(true); setTimeout(() => { audioRef.current?.play(); }, 300); } }, [eaten]);

return ( <div className="min-h-screen flex items-center justify-center bg-gradient-to-b from-pink-500 to-red-600 p-4 relative overflow-hidden"> <audio ref={audioRef} src="/palagi-chorus.mp3" /> <Card className="rounded-2xl shadow-xl"> <CardContent className="p-4"> <h1 className="text-xl font-bold text-center mb-2">🐍 Valentine Snake</h1> <div className="grid bg-black" style={{ gridTemplateColumns: repeat(${GRID_SIZE}, 1fr), width: 300, height: 300, }} > {Array.from({ length: GRID_SIZE * GRID_SIZE }).map((_, i) => { const x = i % GRID_SIZE; const y = Math.floor(i / GRID_SIZE); const isSnake = snake.some((s) => s.x === x && s.y === y); const isFood = food.x === x && food.y === y; return ( <div key={i} className={border border-gray-800 ${ isSnake ? "bg-green-400" : isFood ? "bg-pink-400" : "bg-black" }} /> ); })} </div> <p className="text-center mt-2 text-sm">Dots eaten: {eaten} / 5</p> </CardContent> </Card>

<AnimatePresence>
    {showPopup && !celebrate && (
      <motion.div
        initial={{ opacity: 0, scale: 0.8 }}
        animate={{ opacity: 1, scale: 1 }}
        exit={{ opacity: 0 }}
        className="absolute inset-0 bg-black/60 flex items-center justify-center"
      >
        <Card className="rounded-2xl p-6 text-center max-w-sm">
          <img
            src="/valentine-photo.jpg"
            alt="Us"
            className="w-32 h-32 object-cover rounded-xl mx-auto mb-4"
          />
          <h2 className="text-2xl font-bold mb-4">💖 My Ibyang, Will you be my Valentine?</h2>
          <div className="flex gap-4 justify-center">
            <Button onClick={() => setCelebrate(true)}>Yes 💕</Button>
            <Button disabled className="opacity-40 cursor-not-allowed">
              No 💔
            </Button>
          </div>
        </Card>
      </motion.div>
    )}
  </AnimatePresence>

  <AnimatePresence>
    {celebrate && (
      <motion.div
        initial={{ opacity: 0 }}
        animate={{ opacity: 1 }}
        className="absolute inset-0 flex items-center justify-center"
      >
        <div className="text-center space-y-4">
          <motion.h1
            initial={{ scale: 0 }}
            animate={{ scale: 1 }}
            className="text-4xl font-extrabold text-white"
          >
            🎉 SHE SAID YES! 💕
          </motion.h1>
          <div className="flex justify-center gap-2 text-3xl">
            💖 💘 💝 💞 💓
          </div>
        </div>
      </motion.div>
    )}
  </AnimatePresence>
</div>

); }# snakegame
