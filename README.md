 import { useState, useEffect } from "react";
import { db } from "./firebaseConfig"; // Configuração do Firebase
import { collection, addDoc, getDocs } from "firebase/firestore";
import { Card, CardContent } from "@/components/ui/card";
import { Button } from "@/components/ui/button";

export default function CourseReviews() {
  const [courses, setCourses] = useState([]);
  const [reviews, setReviews] = useState([]);
  const [selectedCourse, setSelectedCourse] = useState("");
  const [rating, setRating] = useState(5);
  const [comment, setComment] = useState("");

  useEffect(() => {
    async function fetchCourses() {
      const coursesSnapshot = await getDocs(collection(db, "courses"));
      setCourses(coursesSnapshot.docs.map((doc) => ({ id: doc.id, ...doc.data() })));
    }
    fetchCourses();
  }, []);

  const handleSubmit = async () => {
    if (!selectedCourse || !comment) return;
    await addDoc(collection(db, "reviews"), {
      course: selectedCourse,
      rating,
      comment,
      timestamp: new Date(),
    });
    alert("Avaliação enviada!");
  };

  return (
    <div className="p-6">
      <h1 className="text-2xl font-bold mb-4">Avalie um Curso</h1>
      <select className="border p-2" onChange={(e) => setSelectedCourse(e.target.value)}>
        <option value="">Selecione um curso</option>
        {courses.map((course) => (
          <option key={course.id} value={course.id}>{course.name}</option>
        ))}
      </select>
      <input
        type="number"
        min="1"
        max="5"
        value={rating}
        onChange={(e) => setRating(e.target.value)}
        className="border p-2 ml-2"
      />
      <textarea
        placeholder="Deixe seu comentário"
        className="border p-2 block w-full mt-2"
        onChange={(e) => setComment(e.target.value)}
      />
      <Button onClick={handleSubmit} className="mt-2">Enviar Avaliação</Button>
    </div>
  );
}
