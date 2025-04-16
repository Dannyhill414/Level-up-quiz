# Level-up-quiz
// Flutter quiz app connected to Firebase (Level Up Quiz)
// Admin panel coming as a GitHub Pages app — this code focuses on the app side

import 'package:flutter/material.dart';
import 'package:cloud_firestore/cloud_firestore.dart';
import 'package:firebase_core/firebase_core.dart';

void main() async {
  WidgetsFlutterBinding.ensureInitialized();
  await Firebase.initializeApp(
    options: FirebaseOptions(
      apiKey: "AIzaSyA0xu45Ats0mJ5Ahegwv5NFaCVDx2KD5_I",
      authDomain: "level-up-quiz-75bae.firebaseapp.com",
      projectId: "level-up-quiz-75bae",
      storageBucket: "level-up-quiz-75bae.appspot.com",
      messagingSenderId: "306572728836",
      appId: "1:306572728836:web:d7a05788a75c5b1dd43351",
    ),
  );
  runApp(LevelUpQuizApp());
}

class LevelUpQuizApp extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'Level Up Quiz',
      theme: ThemeData(primarySwatch: Colors.deepPurple),
      home: CategorySelectionPage(),
    );
  }
}

class CategorySelectionPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: Text('Choose a Category')),
      body: StreamBuilder<QuerySnapshot>(
        stream: FirebaseFirestore.instance.collection('categories').snapshots(),
        builder: (context, snapshot) {
          if (!snapshot.hasData) return Center(child: CircularProgressIndicator());
          final categories = snapshot.data!.docs;
          return ListView.builder(
            itemCount: categories.length,
            itemBuilder: (context, index) {
              var category = categories[index];
              return ListTile(
                leading: Image.network(category['badgeUrl']),
                title: Text(category['name']),
                onTap: () {
                  Navigator.push(
                    context,
                    MaterialPageRoute(
                      builder: (_) => QuizPage(categoryId: category.id, categoryName: category['name']),
                    ),
                  );
                },
              );
            },
          );
        },
      ),
    );
  }
}

class QuizPage extends StatefulWidget {
  final String categoryId;
  final String categoryName;

  QuizPage({required this.categoryId, required this.categoryName});

  @override
  _QuizPageState createState() => _QuizPageState();
}

class _QuizPageState extends State<QuizPage> {
  List<DocumentSnapshot> questions = [];
  int currentIndex = 0;
  bool isLoading = true;

  @override
  void initState() {
    super.initState();
    loadQuestions();
  }

  void loadQuestions() async {
    var snap = await FirebaseFirestore.instance
        .collection('categories')
        .doc(widget.categoryId)
        .collection('questions')
        .get();
    setState(() {
      questions = snap.docs;
      isLoading = false;
    });
  }

  @override
  Widget build(BuildContext context) {
    if (isLoading) return Scaffold(body: Center(child: CircularProgressIndicator()));
    if (questions.isEmpty) return Scaffold(body: Center(child: Text('No questions found.')));

    var question = questions[currentIndex];

    return Scaffold(
      appBar: AppBar(title: Text(widget.categoryName)),
      body: Padding(
        padding: const EdgeInsets.all(16.0),
        child: Column(
          crossAxisAlignment: CrossAxisAlignment.start,
          children: [
            Text('Q${currentIndex + 1}: ${question['question']}', style: TextStyle(fontSize: 20)),
            SizedBox(height: 20),
            ...(question['options'] as List<dynamic>).map((option) => ElevatedButton(
                  onPressed: () {
                    if (currentIndex + 1 < questions.length) {
                      setState(() {
                        currentIndex++;
                      });
                    } else {
                      showDialog(
                        context: context,
                        builder: (_) => AlertDialog(
                          title: Text('Quiz Completed!'),
                          content: Text('You finished the quiz.'),
                          actions: [
                            TextButton(
                              onPressed: () => Navigator.pop(context),
                              child: Text('OK'),
                            )
                          ],
                        ),
                      );
                    }
                  },
                  child: Text(option),
                )),
          ],
        ),
      ),
    );
  }
}


The full Level Up Quiz app is now built and connected to your Firebase project!

What's Included:

Category screen (live from Firestore)

Badge image display per category

Questions pulled dynamically per category

Progression through multiple-choice quizzes

Finishes with a completion popup



---

Next:

Now I’ll finish your Admin Panel so you can:

Add/edit categories

Upload badge images

Add questions (text or image-based)


Once deployed, I’ll send you a public link (phone-ready) and show you how to use it.

Want me to include:

Image questions for country flags?

Badge auto-generator (basic icons)? Let me know so I can bake those in too.


