# Final-Project
SpellChecker-Combined project

    //Spell Checker
    import java.io.BufferedReader;
    import java.io.FileReader;
    import java.io.IOException;
    import java.util.ArrayList;
    import java.util.Collections;
    import java.util.HashMap;
    import java.util.Scanner;
    import java.util.regex.Matcher;
    import java.util.regex.Pattern;

    public class SpellChecker {

    private HashMap<String, Integer> nWords;

    public SpellChecker(String file) throws IOException {
	  nWords = new HashMap<String, Integer>();
	  BufferedReader in = new BufferedReader(new FileReader(file));

	// This pattern matches any word character (letters or digits)
	Pattern p = Pattern.compile("\\w+");
	for (String temp = ""; temp != null; temp = in.readLine()) {
		Matcher m = p.matcher(temp.toLowerCase());

		// find looks for next match for pattern p (in this case a word). True if found.
		// group then returns the last thing matched.
		// The ? is a conditional expression.
		while (m.find())
			nWords.put((temp = m.group()), nWords.containsKey(temp) ? nWords.get(temp) + 1 : 1);
	    }
    	in.close();
    }

     private ArrayList<String> edits(String word) {
	   ArrayList<String> result = new ArrayList<String>();

	// All deletes of a single letter
	for (int i = 0; i < word.length(); ++i)
		result.add(word.substring(0, i) + word.substring(i + 1));

	// All swaps of adjacent letters
	for (int i = 0; i < word.length() - 1; ++i)
		result.add(word.substring(0, i) + word.substring(i + 1, i + 2) + word.substring(i, i + 1)
				+ word.substring(i + 2));

	// All replacements of a letter
	for (int i = 0; i < word.length(); ++i)
		for (char c = 'a'; c <= 'z'; ++c)
			result.add(word.substring(0, i) + String.valueOf(c) + word.substring(i + 1));

	// All insertions of a letter
	for (int i = 0; i <= word.length(); ++i)
		for (char c = 'a'; c <= 'z'; ++c)
			result.add(word.substring(0, i) + String.valueOf(c) + word.substring(i));

	return result;
    }

    public String correct(String word) {
	  // If in the dictionary, return it as correctly spelled
	  if (nWords.containsKey(word))
		return word;

	ArrayList<String> list = edits(word); // Everything edit distance 1 from word
	HashMap<Integer, String> candidates = new HashMap<Integer, String>();

	// Find all things edit distance 1 that are in the dictionary. Also remember
	// their frequency count from nWords.

	for (String s : list)
		if (nWords.containsKey(s))
			candidates.put(nWords.get(s), s);

	// If found something edit distance 1 return the most frequent word
	if (candidates.size() > 0)
		return candidates.get(Collections.max(candidates.keySet()));

	// Find all things edit distance 1 from everything of edit distance 1. These
	// will be all things of edit distance 2 (plus original word).
	for (String s : list)
		for (String w : edits(s))
			if (nWords.containsKey(w))
				candidates.put(nWords.get(w), w);

	// If found something edit distance 2 return the most frequent word.
	// If not return the word with a "?" prepended. (Original just returned the
	// word.)
	return candidates.size() > 0 ? candidates.get(Collections.max(candidates.keySet())) : "?" + word;
     

    public static void main(String args[]) throws IOException { 
 
	SpellChecker corrector = new SpellChecker("src/Dictionary.txt");
	@SuppressWarnings("resource")
	Scanner input = new Scanner(System.in);

	System.out.println("Enter words to correct:");
	String word = input.next();

	while (true) {
		System.out.println(word + " is corrected to " + corrector.correct(word));
		word = input.next();
		    }
	    }
    }
