import java.util.*;
// ---------- Book Class ----------
class Book {
 private String bookId, title, author, category;
 private boolean available = true;
 public Book(String bookId, String title, String author, String category) {
 this.bookId = bookId;
 this.title = title;
 this.author = author;
 this.category = category;
 }
 public String getBookId() { return bookId; }
 public String getTitle() { return title; }
 public String getAuthor() { return author; }
 public boolean isAvailable() { return available; }
 public void setAvailable(boolean available) { this.available = available; }
 @Override
 public String toString() {
 return "["+bookId+"] " + title + " by " + author + " ("+category+") " +
 (available?"Available":"Issued");
 }
}// ---------- Member Base Class ----------
abstract class Member {
 protected String memberId, name, email, phone;
 protected List<Book> borrowed = new ArrayList<>();
 public Member(String id, String name, String email, String phone) {
 this.memberId=id; this.name=name; this.email=email; this.phone=phone;
 }
 public abstract int getBorrowLimit();
 public abstract double calcFine(int lateDays);
 public boolean borrow(Book b) {
 if(borrowed.size()<getBorrowLimit() && b.isAvailable()) {
 borrowed.add(b);
 b.setAvailable(false);
 return true;
 }
 return false;
 }
 public boolean returnBook(Book b,int lateDays) {
 if(borrowed.remove(b)) {
 b.setAvailable(true);
 double fine=calcFine(lateDays);
 if(fine>0) System.out.println("Fine for "+name+": Rs."+fine);
 return true;
 }return false;
 }
 public void listBorrowed() {
 System.out.println(name+" borrowed: "+borrowed);
 }
}
// ---------- Student Member ----------
class StudentMember extends Member {
 private String semester, dept;
 private int borrowLimit;
 public StudentMember(String id,String n,String e,String p,String sem,String d) {
 super(id,n,e,p);
 this.semester=sem; this.dept=d; this.borrowLimit=3;
 }
 public int getBorrowLimit(){ return borrowLimit; }
 public double calcFine(int lateDays){ return lateDays*2.0; }
}
// ---------- Faculty Member ----------
class FacultyMember extends Member {
 private String designation;
 private int borrowLimit; private int priority;
 public FacultyMember(String id,String n,String e,String p,String desig)super(id,n,e,p);
 this.designation=desig; this.borrowLimit=5; this.priority=1;
 }
 public int getBorrowLimit(){ return borrowLimit; }
 public double calcFine(int lateDays){ return lateDays*1.0; }
}
// ---------- Library Service ----------
class LibraryService {
 private List<Book> books=new ArrayList<>();
 private List<Member> members=new ArrayList<>();
 public void addBook(Book b){ books.add(b); }
 public void addMember(Member m){ members.add(m); }
 // Overloaded search()
 public List<Book> search(String title){
 List<Book> r=new ArrayList<>();
 for(Book b:books) if(b.getTitle().equalsIgnoreCase(title)) r.add(b);
 return r;
 }
 public List<Book> searchByAuthor(String author){
 List<Book> r=new ArrayList<>();
 for(Book b:books) if(b.getAuthor().equalsIgnoreCase(author)) r.add(b);
 return r;
 }
 public Book searchById(String id){for(Book b:books) if(b.getBookId().equals(id)) return b;
 return null;
 }
 public void dailyReport(){
 System.out.println("----- Daily Report -----");
 for(Member m:members) m.listBorrowed();
 }
}
// ---------- Main ----------
public class LibraryAppMain {
 public static void main(String[] args) {
 LibraryService service=new LibraryService();
 // Add Books
 service.addBook(new Book("B1","Java","Gosling","Programming"));
 service.addBook(new Book("B2","AI Basics","Russell","AI"));
 service.addBook(new Book("B3","Data Science","Smith","DS"));
 // Add Members
 Member s1=new StudentMember("S1","Alice","a@x.com","999","Sem3","CSE");
 Member f1=new FacultyMember("F1","Dr.Bob","b@x.com","888","Professor");
 service.addMember(s1); service.addMember(f1);
 // Issue Books
 Book b1=service.searchById("B1");
 s1.borrow(b1);Book b2=service.searchById("B2");
 f1.borrow(b2);
 // Return with late days
 s1.returnBook(b1,3); // fine = 6
 f1.returnBook(b2,3); // fine = 3
 // Search
 System.out.println("Search Java: "+service.search("Java"));
 System.out.println("Search by Author Gosling: "+service.searchByAuthor("Gosling"));
 // Daily Report
 service.dailyReport();
 }
}
