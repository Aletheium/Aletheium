// App.jsx import React, { useState, useEffect } from 'react'; import { Sun, Moon, Flame, Lightbulb, ThumbsUp, Meh, XCircle } from 'lucide-react'; import './index.css';

const dummyUsers = [ { id: 1, name: 'Jane Doe', username: 'jane', answers: 24 }, { id: 2, name: 'John Smith', username: 'john', answers: 12 }, ];

const dailyChallenge = { id: 0, text: 'If you could ask one question to a historical figure, who would it be and what would you ask?', tags: ['Philosophy', 'History'], };

export default function App() { const [theme, setTheme] = useState('system'); const [user, setUser] = useState(null); const [questions, setQuestions] = useState([dailyChallenge]); const [newQ, setNewQ] = useState(''); const [tag, setTag] = useState(''); const [filterTag, setFilterTag] = useState('');

useEffect(() => { const userTheme = localStorage.getItem('theme') || 'system'; setTheme(userTheme); document.documentElement.className = userTheme === 'system' ? (window.matchMedia('(prefers-color-scheme: dark)').matches ? 'dark' : '') : userTheme; }, []);

const toggleTheme = () => { const nextTheme = theme === 'dark' ? 'light' : 'dark'; setTheme(nextTheme); localStorage.setItem('theme', nextTheme); document.documentElement.className = nextTheme === 'dark' ? 'dark' : ''; };

const login = (username) => { const u = dummyUsers.find(u => u.username === username); if (u) setUser(u); };

const askQuestion = () => { if (newQ.trim()) { setQuestions([{ id: Date.now(), text: newQ, tags: tag.split(','), answers: [] }, ...questions]); setNewQ(''); setTag(''); } };

const answerQuestion = (qid, answer) => { setQuestions(qs => qs.map(q => q.id === qid ? { ...q, answers: [...(q.answers || []), { by: user.name, text: answer, emoji: {} }] } : q ) ); };

const addReaction = (qid, index, type) => { const updated = [...questions]; const answer = updated.find(q => q.id === qid).answers[index]; answer.emoji[type] = (answer.emoji[type] || 0) + 1; setQuestions(updated); };

return ( <div className="min-h-screen bg-white dark:bg-zinc-900 text-zinc-900 dark:text-white p-4"> <div className="flex justify-between items-center mb-4"> <h1 className="text-2xl font-bold">🧠 Aletheium</h1> <div className="flex items-center gap-4"> <button onClick={toggleTheme}>{theme === 'dark' ? <Sun /> : <Moon />}</button> {!user ? ( <select onChange={e => login(e.target.value)} className="bg-zinc-200 dark:bg-zinc-700 p-1 rounded"> <option value="">Login</option> {dummyUsers.map(u => <option key={u.username} value={u.username}>{u.name}</option>)} </select> ) : ( <span>👤 {user.name}</span> )} </div> </div>

<div className="mb-6">
    <h2 className="text-xl font-semibold mb-2">📌 Daily Challenge</h2>
    <QuestionCard q={dailyChallenge} />
  </div>

  {user && (
    <div className="mb-6">
      <textarea
        value={newQ}
        onChange={e => setNewQ(e.target.value)}
        placeholder="Ask a question..."
        className="w-full p-2 rounded bg-zinc-100 dark:bg-zinc-800"
      />
      <input
        value={tag}
        onChange={e => setTag(e.target.value)}
        placeholder="Tags (comma-separated)"
        className="w-full p-2 mt-2 rounded bg-zinc-100 dark:bg-zinc-800"
      />
      <button onClick={askQuestion} className="mt-2 px-4 py-1 bg-blue-600 text-white rounded">Ask</button>
    </div>
  )}

  <div className="mb-4">
    <label>Filter by Tag: </label>
    <input
      value={filterTag}
      onChange={e => setFilterTag(e.target.value)}
      className="ml-2 p-1 rounded bg-zinc-100 dark:bg-zinc-800"
    />
  </div>

  <div className="grid gap-4">
    {questions
      .filter(q => !filterTag || q.tags?.includes(filterTag))
      .map(q => <QuestionCard key={q.id} q={q} onAnswer={answer => answerQuestion(q.id, answer)} onReact={addReaction} />)}
  </div>
</div>

); }

function QuestionCard({ q, onAnswer, onReact }) { const [ans, setAns] = useState(''); return ( <div className="border border-zinc-300 dark:border-zinc-700 p-4 rounded-xl shadow-sm"> <h3 className="text-lg font-semibold">{q.text}</h3> <div className="text-sm text-zinc-500">{q.tags?.map(t => #${t}).join(' ')}</div> <div className="my-2 text-xs italic text-green-500">TL;DR: Placeholder summary...</div> {q.answers?.map((a, i) => ( <div key={i} className="bg-zinc-100 dark:bg-zinc-800 p-2 rounded mt-2"> <div className="font-medium">Anonymous | {Math.floor(Math.random()*30)} answers in this tag</div> <div>{a.text}</div> <div className="flex gap-2 mt-1 text-sm"> <button onClick={() => onReact(q.id, i, 'like')}><ThumbsUp size={16} /></button> <button onClick={() => onReact(q.id, i, 'fire')}><Flame size={16} /></button> <button onClick={() => onReact(q.id, i, 'light')}><Lightbulb size={16} /></button> <button onClick={() => onReact(q.id, i, 'meh')}><Meh size={16} /></button> <button onClick={() => onReact(q.id, i, 'x')}><XCircle size={16} /></button> </div> </div> ))} {onAnswer && ( <div className="mt-2"> <textarea value={ans} onChange={e => setAns(e.target.value)} placeholder="Write your answer..." className="w-full p-2 rounded bg-zinc-50 dark:bg-zinc-700" /> <button onClick={() => { onAnswer(ans); setAns(''); }} className="mt-1 px-3 py-1 bg-green-600 text-white rounded">Answer</button> </div> )} </div> ); }

