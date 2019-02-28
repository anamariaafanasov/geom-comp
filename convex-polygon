#include <iostream>
#include <cmath>
using namespace std;

#define INF 10000

struct Point
{
    double x;
    double y;
};

typedef struct elem {
    Point val;
    elem *next, *prev;
}node;

bool onSegment(Point p, Point q, Point r)
{
    if (q.x <= max(p.x, r.x) && q.x >= min(p.x, r.x) &&
            q.y <= max(p.y, r.y) && q.y >= min(p.y, r.y))
        return true;
    return false;
}

int orientation(Point p, Point q, Point r)
{
    double val = (q.y - p.y) * (r.x - q.x) -
              (q.x - p.x) * (r.y - q.y);

    if (val == 0) return 0;  // colinear
    return (val > 0)? 1: 2; // clock or counterclock wise
}

void replaceNode(node *oldNode, node *newNode) {
    newNode->prev = oldNode->prev;
    newNode->next = oldNode->next;
    oldNode->prev->next = newNode;
    oldNode->next->prev = newNode;
}


bool doIntersect(Point p1, Point q1, Point p2, Point q2)
{

    int o1 = orientation(p1, q1, p2);
    int o2 = orientation(p1, q1, q2);
    int o3 = orientation(p2, q2, p1);
    int o4 = orientation(p2, q2, q1);


    if (o1 != o2 && o3 != o4)
        return true;


    if (o1 == 0 && onSegment(p1, p2, q1)) return true;


    if (o2 == 0 && onSegment(p1, q2, q1)) return true;

    if (o3 == 0 && onSegment(p2, p1, q2)) return true;


    if (o4 == 0 && onSegment(p2, q1, q2)) return true;

    return false;
}


bool isInside(Point polygon[], int n, Point p)
{

    if (n < 3)  return false;


    Point extreme = {INF, p.y};

    int count = 0, i = 0;
    do
    {
        int next = (i+1)%n;

        if (doIntersect(polygon[i], polygon[next], p, extreme))
        {
            if (orientation(polygon[i], p, polygon[next]) == 0)
               return onSegment(polygon[i], p, polygon[next]);

            count++;
        }
        i = next;
    } while (i != 0);
    bool isYMatch = false;
    for(int i = 0; i < n; i++)
        if(polygon[i].y == p.y)
            isYMatch = true;
    count -= isYMatch && (count > 0);

    return count&1;
}

int GetMatches(Point p1, Point outsider, Point p2)              ///matches poate fi maxim 2
{
    int matches = 0;
    if(p1.x <= p2.x && outsider.x >= p1.x && outsider.x <= p2.x)
        matches++;
    if(p1.y <= p2.y && outsider.y >= p1.y && outsider.y <= p2.y)
        matches++;
    if(p1.x >= p2.x && outsider.x <= p1.x && outsider.x >= p2.x)
        matches++;
    if(p1.y >= p2.y && outsider.y <= p1.y && outsider.y >= p2.y)
        matches++;
    return matches;
}

double getPointsDistance(Point pct1, Point pct2)
{
    return sqrt(pow((pct1.y - pct2.y),2) + pow((pct1.x - pct2.x),2));
}

void printPolygon(node *start) {
    if(start == NULL)
        return;
    node *current = start;
    do{
        cout << current->val.x << ' ' << current->val.y << '\n';
        current = current->next;
    }while(current != NULL && current != start);
}


int main()
{
    Point polygon1[] = {{4,8}, {4.5,6}, {5,4}, {6.5,6}, {7,8},{5,10}};
    int polSize = sizeof(polygon1)/sizeof(polygon1[0]);
    node *polStart = new node();
    polStart->val = polygon1[0];
    polStart->next = NULL;
    polStart->prev = NULL;

    node *polEnd = polStart;
    for(int i = 1; i < polSize; i++) {
        node *newNode = new node;
        newNode->val = polygon1[i];
        newNode->prev = polEnd;
        newNode->next = NULL;
        polEnd->next = newNode;
        polEnd = newNode;
    }
    if(polEnd != polStart) {
        polStart->prev = polEnd;
        polEnd->next = polStart;
    }

    printPolygon(polStart);
    int insertPos;
    Point p = {5, -5};
    node *newNode = new node;
    newNode->next = NULL;
    newNode->prev = NULL;
    newNode->val = p;
    if(isInside(polygon1, polSize, p))
    {
        cout << " punctul se afla in interiorul poligonului " << " \n" << "acoperirea convexa este chiar poligonul initial";
        return 0;
    }
    else
    {
        node *closestRef = polStart;
        node *current = polStart->next;
        double minDist = getPointsDistance(polStart->val, newNode->val);
        while(current != polStart) {
            //cout << "\nDistance between: " << current->val.x << ' ' << current->val.y << " _ " << newNode->val.x << ' ' << newNode->val.y;
            double currentDistance = getPointsDistance(current->val, newNode->val);
            //cout << "is " << currentDistance;
            if(currentDistance < minDist) {
                closestRef = current;
                //cout << "CLOSEST REF : " << closestRef->val.x << " " << closestRef->val.y << "\n";
                minDist = currentDistance;
            }
            current = current->next;
        }
        node *prevRef, *nextRef;
        prevRef = closestRef->prev;
        nextRef = closestRef->next;
        ///avem de ales dintre doua segm : prev_poz, poz_min si poz_min, next_poz
        ///daca segmentele au acelasi nr de match-uri inseamna ca punctul de pe poz_min poate fi inlocuit cu outsider-ul(p)
        int matches_prev = 0, matches_next = 0;
        matches_prev = GetMatches(prevRef->val, newNode->val, closestRef->val);
        matches_next = GetMatches(closestRef->val, newNode->val, nextRef->val);
        //cout << "\nmatches prev: " << matches_prev;
        //cout << "\nmatches next: " << matches_next;
        //cout << "Replacing " << closestRef->val.x << ' ' << closestRef->val.y;
        //return 0;
        if(matches_next == matches_prev || closestRef->val.x == newNode->val.x || closestRef->val.y == newNode->val.y)        ///nu pot fi simultan egale cu 0
        {

            replaceNode(closestRef, newNode);

        }
        else {
            int dir = matches_prev < matches_next ? -1 : 1; //-1 interval prec, 1 interval succ
            if((closestRef == polStart && dir == -1) || (closestRef == polEnd && dir == 1)) {
                if(p.x < polygon1[0].x) {
                    //update polStart
                    polEnd->next = closestRef;
                    closestRef->prev = polEnd;
                    closestRef->next = polStart;
                    polStart->prev = closestRef;
                    polStart = closestRef;
                }
                else {
                    //update polEnd
                    polEnd->next = closestRef;
                    closestRef->prev = polEnd;
                    closestRef->next = polStart;
                    polStart->prev = closestRef;
                    polEnd = closestRef;
                }
            } else {
                ///dir == -1 => prev_poz p poz_min
                ///dir == 1  => poz_min p prev_poz
                if(dir == -1) {
                    closestRef->prev->next = newNode;
                    newNode->prev = closestRef->prev;
                    newNode->next = closestRef;
                    closestRef->prev = newNode;
                } else {
                    closestRef->next->prev = newNode;
                    newNode->prev = closestRef;
                    newNode->next = closestRef->next;
                    closestRef->next = newNode;
                }
                //insert in polygon1 pe pozitia insertPos
            }
        }

        bool isLeftCurbClockwise = orientation(newNode->prev->prev->val, newNode->prev->val, newNode->val) == 1;
        bool isRightCurbClockwise = orientation(newNode->val, newNode->next->val, newNode->next->next->val) == 1;
        while(isLeftCurbClockwise) {
            //remove newNode->prev
            newNode->prev->prev->next = newNode;
            newNode->prev = newNode->prev->prev;
            isLeftCurbClockwise = orientation(newNode->prev->prev->val, newNode->prev->val, newNode->val) == 1;
        }
        while(isRightCurbClockwise) {
            //remove newNode->next
            newNode->next = newNode->next->next;
            newNode->next->next->prev = newNode;
            isRightCurbClockwise = orientation(newNode->val, newNode->next->val, newNode->next->next->val) == 1;
        }
        cout << "\nSolutia finala:\n";
        printPolygon(polStart);
    }

    return 0;
}
