#include <stdio.h>
#include <stdlib.h>
#include <string.h>

int distance[20][20];
int count[20];
int cur_no_of_drivers=0;
int all_dist[20];

// Structure to represent a node in the adjacency list
struct Node {
    char data;
    struct Node* next;
};
struct Graph {
    int numVertices;
    struct Node** adjLists;
};
struct cus_node{
    char name[50];
    char contact_no[10];
    struct cus_node* next;
    
};
struct Driver
{
    char name[50];
    char vehicle_no[10];
    int capacity;
    char contact_no[10];
    int no_of_nodes;
    struct Node* holder;
    struct cus_node* cur_customer;
};

struct DriverNode
{
    struct Driver driver;
    struct DriverNode* next;
};



struct Node* createNode(char data)
{
    struct Node* newNode = (struct Node*)malloc(sizeof(struct Node));
    newNode->data = data;
    newNode->next = NULL;
    return newNode;
}

struct Node* insert_node(struct Node* head,struct Node* newnode)
{
    if(head==NULL)
        return newnode;
    else
    {
        struct Node*cur = head;
        while(cur->next!=NULL)
        {
            cur = cur->next;
        }
        cur->next = newnode;
        return head;
    }
        
    
}
// Function to create a graph with a given number of vertices
struct Graph* createGraph(int numVertices) 
{
    struct Graph* graph = (struct Graph*)malloc(sizeof(struct Graph));
    graph->numVertices = numVertices;
    graph->adjLists = (struct Node**)malloc(numVertices * sizeof(struct Node*));

    for (int i = 0; i < numVertices; i++)
    {
        graph->adjLists[i] = NULL;
    }

    return graph;
}

void print_list(struct Node*head)
{
    struct Node*cur = head;
    while(cur!=NULL)
    {
        printf("%c-->",cur->data);
        cur = cur->next;
    }
}


struct DriverNode* insertDriver(struct DriverNode* head, struct DriverNode* newNode)
                
{
    
    if (head == NULL)
    {
        return newNode;
    }
    struct DriverNode* current = head;
    while (current->next != NULL)
    {
        current = current->next;
    }
    current->next = newNode;
    return head;
}

// Function to delete a specified driver from the linked list
struct DriverNode* deleteDriver(struct DriverNode* head, char* name)
{
    if (head == NULL)
    {
        return NULL; // List is empty
    }
    if (strcmp(head->driver.name, name) == 0) {
        struct DriverNode* newHead = head->next;
        free(head);
        return newHead;
    }
    struct DriverNode* current = head;
    while (current->next != NULL) {
        if (strcmp(current->next->driver.name, name) == 0) {
            struct DriverNode* temp = current->next;
            current->next = temp->next;
            free(temp);
            return head;
        }
        current = current->next;
    }
    return head;
}
void print_cust(struct cus_node* head)

{
    struct cus_node* temp = head;
    while(temp!=NULL)
    {
        printf("%s",temp->name);
        printf("\n");
        temp = temp->next;
    }
}
// Function to print the list of drivers
void printDrivers(struct DriverNode* head)
{
    struct DriverNode* current = head;
    while (current != NULL)
    {
        printf("Driver Name: %s\n", (current->driver).name);
        printf("Vehicle Number: %s\n", (current->driver).vehicle_no);
        printf("Vehicle capacity: %d\n", (current->driver).capacity);
        printf("Contact Number: %s\n", (current->driver).contact_no);
        printf("path of the driver");
        print_list((current->driver).holder);
        printf("\n");
        if((current->driver).cur_customer!=NULL)
        {
            printf("the list of customers are:");
            print_cust((current->driver).cur_customer);
            
        }
        current = current->next;
    }
}

// Function to add an edge between two vertices
void createEdge(struct Graph* graph, char src, char dest,int dist) {
    int srcIndex = src - 'A';
    int destIndex = dest - 'A';
    
    distance[srcIndex][count[srcIndex]] = dist;
    count[srcIndex] = count[srcIndex]+1;
    
    distance[destIndex][count[destIndex]] = dist;
    count[destIndex] = count[destIndex]+1;

    // Add edge from src to dest
    struct Node* newNode = createNode(dest);
    struct Node* temp =graph->adjLists[srcIndex];
    if(temp==NULL)
    {
        graph->adjLists[srcIndex] = newNode;
    }
    else
    {
        while(temp->next!=NULL)
        {
            temp= temp->next;
        }
        temp->next = newNode;
    }
    

    // Add edge from dest to src (undirected graph)
    newNode = createNode(src);
    temp =graph->adjLists[destIndex];
    if(temp==NULL)
    {
        graph->adjLists[destIndex] = newNode;
    }
    else
    {
        while(temp->next!=NULL)
        {
            temp= temp->next;
        }
        temp->next = newNode;
    }
    
    
}

// Function to print the graph
void printGraph(struct Graph* graph) {
    for (int i = 0; i < graph->numVertices; i++) {
        struct Node* currentNode = graph->adjLists[i];
        printf("%c", 'A' + i);
        while (currentNode != NULL) {
            printf("----%c", currentNode->data);
            currentNode = currentNode->next;
        }
        printf("\n");
    }
}
int check_same_path(struct Node* dn,char src,char dest)
{
    struct Node* temp = dn;
    int k = 0;
    while(temp!=NULL)
    {
        if(temp->data == src)
        {
            k = 1;
        }
        if(k==1 && temp->data == dest)
        {
            return 1;
        }
        
        temp = temp->next;
    }
    return 0;
    
    
}
int distance_between_adj_nodes(struct Graph* graph,char src,char dest)
{
    int srcIndex = src - 'A';
    int y = 0;
    struct Node* temp = graph->adjLists[srcIndex];
    while(temp->data!=dest && y!=count[srcIndex])
    {
        y++;
        temp = temp->next;
    }
    return distance[srcIndex][y];
}
int find_distance(struct Graph* graph,struct Node* hea,char src,char dest)
{
    int total = 0;
    struct Node* let = hea;
    while(let->data!=src)
    {
        let = let->next;
    }
    while((let->next)->data!=dest)
    {
        
        total = total + distance_between_adj_nodes(graph, let->data, (let->next)->data);
        let = let->next;
    }
    total = total + distance_between_adj_nodes(graph, let->data, (let->next)->data);
    return total;
}

void driver_allocation(struct Graph* graph,struct DriverNode* head,char src,char dest)
{
    struct DriverNode* temp_driver = head;
    printf("Source:%c",src);
    printf("\n");
    printf("destination:%c",dest);
    printf("\n");
    printf("Available list of drivers are:\n");
    while(temp_driver!=NULL)
    {
        if(check_same_path(((temp_driver->driver).holder),src,dest))
        {
            printf("Driver Name: %s\n", (temp_driver->driver).name);
            printf("Vehicle Number: %s\n", (temp_driver->driver).vehicle_no);
            printf("Vehicle capacity: %d\n", (temp_driver->driver).capacity);
            printf("Contact Number: %s\n", (temp_driver->driver).contact_no);
            printf("path of the driver");
            print_list((temp_driver->driver).holder);
            printf("\n");
            printf("the distance is = %dkm",find_distance(graph,((temp_driver->driver).holder) ,src, dest));
            
            
        }
        printf("\n");
        temp_driver = temp_driver->next;
    }
    
}

struct cus_node* assign_customer_to_driver(struct cus_node* head,struct cus_node* rem)
{
    if(head==NULL)
        return rem;
    else
    {
        struct cus_node*cur = head;
        while(cur->next!=NULL)
        {
            cur = cur->next;
        }
        cur->next = rem;
        return head;
    }
        
    
}
int main()
{
    

    
    struct Graph* graph = createGraph(8);
    createEdge(graph, 'A', 'B',2);
    createEdge(graph, 'A', 'C',3);
    createEdge(graph, 'C', 'D',3);
    createEdge(graph, 'A', 'D',8);
    createEdge(graph, 'D', 'F',2);
    createEdge(graph, 'F', 'E',4);
    createEdge(graph, 'B', 'D',9);
    createEdge(graph, 'B', 'H',5);
    createEdge(graph, 'E', 'H',2);
    createEdge(graph, 'A', 'G',8);
    createEdge(graph, 'B', 'G',3);
    createEdge(graph, 'G', 'H',1);
    createEdge(graph, 'F', 'G',11);
    createEdge(graph, 'B', 'E',2);
    

    // Print the graph
    printf("Graph with 8 locations and current links:\n");
    printGraph(graph);
    
    
    
    int user, y;
    char qw,source,destination;
    char name[100];
    char name2[100];
    struct DriverNode* head = NULL;
    struct DriverNode* driverList;
    struct Node* temp_let1 = NULL;
    struct Node* temp_let2 = NULL;
    struct Node* temp_let3 = NULL;
    //struct DriverNode* current;
    struct DriverNode* temp2;
    struct Node*temp = NULL;
    struct cus_node *tell = NULL;
    
    struct DriverNode* let1 = (struct DriverNode*)malloc(sizeof(struct DriverNode));
    strcpy(((let1->driver).name) , "AYUSH");
    strcpy(((let1->driver).vehicle_no) , "0001");
    strcpy(((let1->driver).contact_no) , "953434321");
    ((let1->driver).capacity) = 4;
    ((let1->driver).no_of_nodes) = 4;
    temp_let1 = createNode('A');
    (let1->driver).holder =insert_node((let1->driver).holder, temp);
    
    temp_let1 = createNode('B');
    (let1->driver).holder =insert_node((let1->driver).holder, temp_let1);
    temp_let1 = createNode('H');
    (let1->driver).holder =insert_node((let1->driver).holder, temp_let1);
    temp_let1 = createNode('E');
    (let1->driver).holder =insert_node((let1->driver).holder, temp_let1);
    ((let1->driver).cur_customer) = NULL;
    head = insertDriver(head, let1);
    
    struct DriverNode* let2 = (struct DriverNode*)malloc(sizeof(struct DriverNode));
    strcpy(((let2->driver).name) , "AMAN");
    strcpy(((let2->driver).vehicle_no) , "0002");
    strcpy(((let2->driver).contact_no) , "834121211");
    ((let2->driver).capacity) = 5;
    ((let2->driver).no_of_nodes) = 4;
    temp_let2 = createNode('A');
    (let2->driver).holder =insert_node((let2->driver).holder, temp_let2);
    
    temp_let2 = createNode('B');
    (let2->driver).holder =insert_node((let2->driver).holder, temp_let2);
    temp_let2 = createNode('E');
    (let2->driver).holder =insert_node((let2->driver).holder, temp_let2);
    temp_let2 = createNode('F');
    (let2->driver).holder =insert_node((let2->driver).holder, temp_let2);
    ((let2->driver).cur_customer) = NULL;
    head = insertDriver(head, let2);
    
    struct DriverNode* let3 = (struct DriverNode*)malloc(sizeof(struct DriverNode));
    strcpy(((let3->driver).name) , "ASIF");
    strcpy(((let3->driver).vehicle_no) , "0003");
    strcpy(((let3->driver).contact_no) , "67234321");
    ((let3->driver).capacity) = 6;
    ((let3->driver).no_of_nodes) = 6;
    temp_let3 = createNode('A');
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    
    temp_let3 = createNode('D');
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    temp_let3 = createNode('F');
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    temp_let3 = createNode('G');
    
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    temp_let3 = createNode('B');
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    temp_let3 = createNode('E');
    (let3->driver).holder =insert_node((let3->driver).holder, temp_let3);
    ((let3->driver).cur_customer) = NULL;
    head = insertDriver(head, let3);
    
    
    
    
    do
    {
        
        
        
        printf("1. insert driver info\t2. delete driver info\t3. print list of drivers 4:See Available drivers with distance  5:To select your driver 6:exit\n");
        scanf("%d", &user);
        switch(user)
        {
                
            case 1:
                driverList = (struct DriverNode*)malloc(sizeof(struct DriverNode));
                (driverList->driver).cur_customer = NULL;
                printf("Enter Driver Name: ");
                scanf("%s", ((driverList->driver).name));
                printf("Enter Vehicle Number: ");
                scanf("%s", (driverList->driver).vehicle_no);
                printf("Enter Vehicle capacity: ");
                scanf("%d", &((driverList->driver).capacity));
                printf("Enter Contact Number: ");
                scanf("%s", (driverList->driver).contact_no);
                printf("enter the no. of nodes you are going to visit");
                scanf("%d",&((driverList->driver).no_of_nodes));
                printf("enter the nodes in A,B,C,D form");
                (driverList->driver).holder = NULL;
                (driverList->driver).holder= (struct Node*)malloc(((driverList->driver).no_of_nodes)*sizeof(struct Node));
                
                
               
                for(y = 0 ; y<((driverList->driver).no_of_nodes);y++)
                {
                    scanf(" %c",&qw);
                    temp = createNode(qw);
                    (driverList->driver).holder =insert_node((driverList->driver).holder, temp);
                }
                driverList->next = NULL;
                head = insertDriver(head, driverList);
                cur_no_of_drivers++;
                        
                        
                break;
                        
                        
                        
            case 2:
                        
                printf("Enter the name of the driver to delete: ");
                scanf("%s", name);
                head = deleteDriver(head, name);
                cur_no_of_drivers--;
                break;
                        
            case 3:
                        
                printf("List of Drivers:\n");
                printDrivers(head);
                break;
                
            case 4:
                printf("enter your source and destination");
                scanf(" %c %c", &source, &destination);

                driver_allocation(graph,head, source, destination);
                
                break;
            case 5:
                printf("enter the name of the driver you want to travel with");
                scanf("%s",name2);
                temp2  = head;
                while(strcmp((temp2->driver).name, name2))
                {
                    temp2 =temp2->next;
                }
                tell = (struct cus_node*)malloc(sizeof(struct cus_node));
                printf("enter your name");
                scanf("%s",tell->name);
                printf("enter your contact_no");
                scanf("%s",tell->contact_no);
                tell->next = NULL;
                (temp2->driver).cur_customer=assign_customer_to_driver((temp2->driver).cur_customer, tell);
                (temp2->driver).capacity =  (temp2->driver).capacity-1;
                
                
                break;
                
               
        }
    }while(user!=6);
    
    return 0;
}

