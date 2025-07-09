/ src/App.jsx
// Make sure you name the file App.jsx or App.js depending on your build setup
import React, { useState } from "react";

export default function App() {
  const [weights, setWeights] = useState({
    safety_weight: 0.4,
    commute_weight: 0.3,
    amenities_weight: 0.2,
    schools_weight: 0.1,
  });
  const [results, setResults] = useState([]);

  const handleChange = (e) => {
    setWeights({
      ...weights,
      [e.target.name]: parseFloat(e.target.value) || 0,
    });
  };

  const handleSubmit = async (e) => {
    e.preventDefault();
    const response = await fetch("/match", {
      method: "POST",
      headers: { "Content-Type": "application/json" },
      body: JSON.stringify(weights),
    });
    const data = await response.json();
    setResults(data);
  };

  return (
    <div>
      <h1>NeighborFit</h1>
      <form onSubmit={handleSubmit}>
        <label>
          Safety Weight: <input type="number" step="0.1" name="safety_weight" value={weights.safety_weight} onChange={handleChange} />
        </label>
        <br />
        <label>
          Commute Weight: <input type="number" step="0.1" name="commute_weight" value={weights.commute_weight} onChange={handleChange} />
        </label>
        <br />
        <label>
          Amenities Weight: <input type="number" step="0.1" name="amenities_weight" value={weights.amenities_weight} onChange={handleChange} />
        </label>
        <br />
        <label>
          Schools Weight: <input type="number" step="0.1" name="schools_weight" value={weights.schools_weight} onChange={handleChange} />
        </label>
        <br />
        <button type="submit">Find Match</button>
      </form>

      <h2>Results:</h2>
      <ul>
        {results.map((n) => (
          <li key={n.name}>{n.name}: {n.score}</li>
        ))}
      </ul>
    </div>
  );
}
