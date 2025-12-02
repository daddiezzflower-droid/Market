# Market
import { useEffect, useState } from "react";

const AdminDashboard = () => {
  const [members, setMembers] = useState([]);
  const [products, setProducts] = useState([]);
  const [ads, setAds] = useState([]);
  const [loading, setLoading] = useState(true);

  const token = localStorage.getItem("adminToken");

  useEffect(() => {
    const fetchData = async () => {
      try {
        const headers = { Authorization: `Bearer ${token}` };

        const [membersRes, productsRes, adsRes] = await Promise.all([
          fetch("http://localhost:5000/api/admin/members", { headers }),
          fetch("http://localhost:5000/api/admin/products", { headers }),
          fetch("http://localhost:5000/api/admin/ads", { headers }),
        ]);

        setMembers(await membersRes.json());
        setProducts(await productsRes.json());
        setAds(await adsRes.json());
      } catch (err) {
        console.error("Error fetching data:", err);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [token]);

  if (!token) {
    window.location.href = "/login"; // redirect if not logged in
    return null;
  }

  if (loading) return <p className="p-8 text-center">Loading dashboard...</p>;

  return (
    <div className="p-8 bg-gray-100 min-h-screen">
      <h1 className="text-3xl font-bold mb-6">Admin Dashboard</h1>

      <section className="mb-8">
        <h2 className="text-xl font-semibold mb-2">Members</h2>
        <table className="w-full border">
          <thead className="bg-gray-200">
            <tr>
              <th className="p-2 border">Email</th>
              <th className="p-2 border">Plan</th>
              <th className="p-2 border">Status</th>
            </tr>
          </thead>
          <tbody>
            {members.map((m) => (
              <tr key={m.id}>
                <td className="p-2 border">{m.email}</td>
                <td className="p-2 border">{m.plan}</td>
                <td className="p-2 border">{m.status}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </section>

      <section className="mb-8">
        <h2 className="text-xl font-semibold mb-2">Products</h2>
        <table className="w-full border">
          <thead className="bg-gray-200">
            <tr>
              <th className="p-2 border">Title</th>
              <th className="p-2 border">Owner</th>
              <th className="p-2 border">Price</th>
              <th className="p-2 border">Status</th>
            </tr>
          </thead>
          <tbody>
            {products.map((p) => (
              <tr key={p.id}>
                <td className="p-2 border">{p.title}</td>
                <td className="p-2 border">{p.ownerEmail}</td>
                <td className="p-2 border">${p.price}</td>
                <td className="p-2 border">{p.status}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </section>

      <section>
        <h2 className="text-xl font-semibold mb-2">Advertisements</h2>
        <table className="w-full border">
          <thead className="bg-gray-200">
            <tr>
              <th className="p-2 border">Title</th>
              <th className="p-2 border">Owner</th>
              <th className="p-2 border">Link</th>
              <th className="p-2 border">Status</th>
            </tr>
          </thead>
          <tbody>
            {ads.map((ad) => (
              <tr key={ad.id}>
                <td className="p-2 border">{ad.title}</td>
                <td className="p-2 border">{ad.ownerEmail}</td>
                <td className="p-2 border">
                  <a href={ad.link} target="_blank" rel="noopener noreferrer" className="text-blue-500 underline">
                    {ad.link}
                  </a>
                </td>
                <td className="p-2 border">{ad.status}</td>
              </tr>
            ))}
          </tbody>
        </table>
      </section>
    </div>
  );
};

export default AdminDashboard;
