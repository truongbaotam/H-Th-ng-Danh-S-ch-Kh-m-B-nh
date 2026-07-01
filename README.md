#include <iostream>
#include <string>

using namespace std;

struct Patient {
    string id;
    string name;
    int age;
    Patient(string i, string n, int a) : id(i), name(n), age(a) {}
};

struct QueueNode {
    Patient* patient;
    QueueNode* next;
    QueueNode(Patient* p) : patient(p), next(nullptr) {}
};

class WaitingQueue {
private:
    QueueNode* frontNode;
    QueueNode* rearNode;
    int count;
public:
    WaitingQueue() : frontNode(nullptr), rearNode(nullptr), count(0) {}

    void enqueue(Patient* p) {
        QueueNode* newNode = new QueueNode(p);
        if (rearNode == nullptr) {
            frontNode = rearNode = newNode;
        }
        else {                                  //
            rearNode->next = newNode;           //Thêm phần tử vào đuôi hàng đợi
            rearNode = newNode;                 //
        }
        count++;
    }

    Patient* dequeue() {
        if (isEmpty()) return nullptr;
        QueueNode* temp = frontNode;
        Patient* p = temp->patient;
        frontNode = frontNode->next;           //Lấy phần tử ra khỏi đầu hàng đợi       1 2
                                                //                                      2 3
        if (frontNode == nullptr) rearNode = nullptr;
        delete temp;
        count--;
        return p;
    }

    bool isEmpty() { return frontNode == nullptr; }
    int size() { return count; }

    void displayQueue() {
        if (isEmpty()) {
            cout << "=> Hang doi dang trong!\n";
            return;
        }
        QueueNode* temp = frontNode;
        int stt = 1;
        while (temp != nullptr) {
            cout << "  " << stt++ << ". STT-Hieu: [" << temp->patient->id << "] - Ten: " << temp->patient->name << " (" << temp->patient->age << " tuoi)\n";
            temp = temp->next;
        }
    }

    ~WaitingQueue() {
        while (!isEmpty()) {
            Patient* p = dequeue();
            delete p;
        }
    }
};

struct BSTNode {
    Patient* patient;
    BSTNode* left;
    BSTNode* right;
    BSTNode(Patient* p) {
        patient = new Patient(p->id, p->name, p->age);    //Khởi tạo một nút mới trên cây BST   
        left = nullptr;
        right = nullptr;
    }
    ~BSTNode() {
        delete patient;
    }
};

class MedicalHistoryBST {
private:
    BSTNode* root;

    BSTNode* insertNode(BSTNode* node, Patient* p) {
        if (node == nullptr) return new BSTNode(p);
        if (p->id < node->patient->id) node->left = insertNode(node->left, p);        //Thêm nút vào cây bên trái (Nhỏ hơn)
        else if (p->id > node->patient->id) node->right = insertNode(node->right, p);
        return node;
    }

    BSTNode* searchNode(BSTNode* node, string id) {
        if (node == nullptr || node->patient->id == id) return node;
        if (id < node->patient->id) return searchNode(node->left, id);
        return searchNode(node->right, id);             //Tìm kiếm trên cây bên phải (Lớn hơn)
    }

    void inorderDisplay(BSTNode* node) {
        if (node == nullptr) return;
        inorderDisplay(node->left);
        cout << "  + Ma BN: " << node->patient->id << " | Ten: " << node->patient->name << " | Tuoi: " << node->patient->age << "\n";
        inorderDisplay(node->right);
    }

    void clearTree(BSTNode* node) {
        if (node == nullptr) return;
        clearTree(node->left);
        clearTree(node->right);
        delete node;
    }
public:
    MedicalHistoryBST() : root(nullptr) {}
    void insert(Patient* p) { root = insertNode(root, p); }
    BSTNode* search(string id) { return searchNode(root, id); }
    void displayAll() { inorderDisplay(root); }
    ~MedicalHistoryBST() { clearTree(root); }
};

int main() {
    system("chcp 65001 > nul");

    WaitingQueue queue;
    MedicalHistoryBST history;

    queue.enqueue(new Patient("BN01", "Nguyen Van A", 25));
    queue.enqueue(new Patient("BN02", "Tran Thi B", 45));
    queue.enqueue(new Patient("BN03", "Le Van C", 60));

    string choiceStr;
    int choice;

    do {
        cout << "\n=============================================\n";
        cout << "    HE THONG QUAN LY DANH SACH CHO KHAM BENH \n";
        cout << "=============================================\n";
        cout << " So luong benh nhan dang cho: " << queue.size() << "\n";
        cout << "---------------------------------------------\n";
        cout << "1. Dang ky cho kham (Enqueue Benh Nhan)\n";
        cout << "2. Goi benh nhan vao kham (Dequeue & Luu lich su)\n";
        cout << "3. Xem danh sach hang doi hien tai\n";
        cout << "4. Tra cuu ho so benh nhan da kham (Search BST)\n";
        cout << "5. Xem toan bo lich su phong kham (Inorder BST)\n";
        cout << "6. Chay kiem thu tu dong (Test Cases)\n";
        cout << "0. Thoat chuong trinh\n";
        cout << "---------------------------------------------\n";
        cout << "Lua chon cua ban: ";

        getline(cin, choiceStr);
        if (choiceStr.empty()) continue;
        try {
            choice = stoi(choiceStr);
        }
        catch (...) {
            cout << "Vui long chi nhap so tu 0 den 6!\n";
            continue;
        }

        switch (choice) {
        case 1: {
            string id, name, ageStr;
            int age;
            cout << "Nhap ma so benh nhan (VD: BN04): ";
            getline(cin, id);
            cout << "Nhap ho va ten benh nhan: ";
            getline(cin, name);
            cout << "Nhap tuoi: ";
            getline(cin, ageStr);
            try {
                age = stoi(ageStr);
            }
            catch (...) {
                cout << "=> Loi: Tuoi phai la mot so nguyen!\n";
                break;
            }
            if (!id.empty() && !name.empty()) {
                queue.enqueue(new Patient(id, name, age));
                cout << "=> Da them benh nhan vao hang doi cho!\n";
            }
            else {
                cout << "=> Loi: Du lieu khong duoc de trong!\n";
            }
            break;
        }
        case 2: {
            Patient* p = queue.dequeue();
            if (p != nullptr) {
                cout << "\n>>> GOI BENH NHAN VAO PHONG KHAM <<<\n";
                cout << "  Ma so: " << p->id << "\n";
                cout << "  Ten: " << p->name << "\n";
                cout << "  Tuoi: " << p->age << "\n";
                cout << "------------------------------------\n";
                history.insert(p);
                delete p;
                cout << "=> Da kham xong va luu ho so vao Cay BST!\n";
            }
            else {
                cout << "=> Khong co benh nhan nao trong hang doi cho!\n";
            }
            break;
        }
        case 3: {
            cout << "\nDanh sach hang doi cho kham hien tai:\n";
            queue.displayQueue();
            break;
        }
        case 4: {
            string searchId;
            cout << "Nhap ma benh nhan can tim kiem ho so: ";
            getline(cin, searchId);
            BSTNode* res = history.search(searchId);
            if (res != nullptr) {
                cout << "=> Tim thay ho so: " << res->patient->name << " (" << res->patient->age << " tuoi)\n";
            }
            else {
                cout << "=> Khong tim thay lich su kham cua benh nhan nay!\n";
            }
            break;
        }
        case 5: {
            cout << "\nToan bo ho so benh nhan da tung kham (Sap xep theo Ma ID):\n";
            history.displayAll();
            break;
        }
        case 6: {
            cout << "\n--- CHAY KIEM THU TU DONG (TEST CASES) ---\n";

            cout << "Test 1: Kiem tra Queue rong khi khoi tao -> ";
            WaitingQueue testQ;
            if (testQ.isEmpty()) cout << "PASSED\n"; else cout << "FAILED\n";

            cout << "Test 2: Kiem tra tinh nang Enqueue -> ";
            testQ.enqueue(new Patient("T1", "Test A", 20));
            if (testQ.size() == 1) cout << "PASSED\n"; else cout << "FAILED\n";

            cout << "Test 3: Kiem tra tinh nang Dequeue (FIFO) -> ";
            Patient* tp = testQ.dequeue();
            if (tp != nullptr && tp->id == "T1") cout << "PASSED\n"; else cout << "FAILED\n";
            delete tp;

            cout << "Test 4: Kiem tra chen vao Cay ho so BST -> ";
            MedicalHistoryBST testTree;
            Patient localP("T2", "Test B", 30);
            testTree.insert(&localP);
            if (testTree.search("T2") != nullptr) cout << "PASSED\n"; else cout << "FAILED\n";

            cout << "Test 5: Kiem tra tim kiem ID khong ton tai tre BST -> ";
            if (testTree.search("NONE") == nullptr) cout << "PASSED\n"; else cout << "FAILED\n";
            break;
        }
        case 0:
            cout << "Cam on ban da su dung chuong trinh!\n";
            break;
        default:
            cout << "Lua chon khong hop le, vui long chon lai.\n";
        }
    } while (choice != 0);

    return 0;
}
