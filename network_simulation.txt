#include <iostream>
#include <vector>
#include <queue>
#include <limits>
using namespace std;

const int INF = numeric_limits<int>::max();

class Network {
private:
    int nodes;
    vector<vector<pair<int, int>>> adj; // (neighbor, cost)

public:
    Network(int n) {
        nodes = n;
        adj.resize(n);
    }

    void addLink(int u, int v, int cost) {
        adj[u].push_back({v, cost});
        adj[v].push_back({u, cost}); // bidirectional
    }

    void simulatePacket(int src, int dest) {
        vector<int> dist(nodes, INF);
        vector<int> parent(nodes, -1);
        priority_queue<pair<int, int>, vector<pair<int, int>>, greater<>> pq;

        dist[src] = 0;
        pq.push({0, src});

        while (!pq.empty()) {
            int u = pq.top().second;
            pq.pop();

            for (auto &edge : adj[u]) {
                int v = edge.first;
                int weight = edge.second;

                if (dist[u] + weight < dist[v]) {
                    dist[v] = dist[u] + weight;
                    parent[v] = u;
                    pq.push({dist[v], v});
                }
            }
        }

        if (dist[dest] == INF) {
            cout << "No path found!\n";
            return;
        }

        // Print path
        vector<int> path;
        for (int v = dest; v != -1; v = parent[v])
            path.push_back(v);

        cout << "Packet path: ";
        for (int i = path.size() - 1; i >= 0; i--)
            cout << path[i] << " ";

        cout << "\nTotal cost (latency): " << dist[dest] << endl;
    }
};

int main() {
    int n = 5;
    Network net(n);

    // Add links (u, v, latency)
    net.addLink(0, 1, 2);
    net.addLink(0, 2, 4);
    net.addLink(1, 2, 1);
    net.addLink(1, 3, 7);
    net.addLink(2, 4, 3);
    net.addLink(3, 4, 1);

    int src = 0, dest = 3;

    cout << "Simulating packet transfer...\n";
    net.simulatePacket(src, dest);

    return 0;
}