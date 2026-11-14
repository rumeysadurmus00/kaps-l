import React, { useState, useEffect } from 'react';

export default function App() {
  const [activeTab, setActiveTab] = useState('wardrobe');
  
  const [clothes, setClothes] = useState(() => {
    const saved = localStorage.getItem('capsule_clothes');
    return saved ? JSON.parse(saved) : [
      { id: 1, name: 'Beyaz Oversize Gömlek', category: 'Üst Giyim', color: '#ffffff', style: 'Günlük' },
      { id: 2, name: 'Mavi Kot Pantolon', category: 'Alt Giyim', color: '#3b82f6', style: 'Günlük' },
      { id: 3, name: 'Bej Trençkot', category: 'Dış Giyim', color: '#d4b28c', style: 'Şık' },
      { id: 4, name: 'Beyaz Sneaker', category: 'Ayakkabı', color: '#ffffff', style: 'Spor' }
    ];
  });

  const [outfits, setOutfits] = useState(() => {
    const saved = localStorage.getItem('capsule_outfits');
    return saved ? JSON.parse(saved) : [];
  });

  const [name, setName] = useState('');
  const [category, setCategory] = useState('Üst Giyim');
  const [color, setColor] = useState('#3b82f6');
  const [style, setStyle] = useState('Günlük');

  const [currentOutfit, setCurrentOutfit] = useState({ üst: null, alt: null, dis: null, ayakkabi: null });
  const [locked, setLocked] = useState({ üst: false, alt: false, dis: false, ayakkabi: false });
  const [outfitName, setOutfitName] = useState('');

  useEffect(() => {
    localStorage.setItem('capsule_clothes', JSON.stringify(clothes));
  }, [clothes]);

  useEffect(() => {
    localStorage.setItem('capsule_outfits', JSON.stringify(outfits));
  }, [outfits]);

  const handleAddCloth = (e) => {
    e.preventDefault();
    if (!name.trim()) return;
    const newCloth = { id: Date.now(), name, category, color, style };
    setClothes([...clothes, newCloth]);
    setName('');
  };

  const handleDeleteCloth = (id) => {
    setClothes(clothes.filter(c => c.id !== id));
  };

  const generateOutfit = () => {
    const categories = {
      'Üst Giyim': clothes.filter(c => c.category === 'Üst Giyim'),
      'Alt Giyim': clothes.filter(c => c.category === 'Alt Giyim'),
      'Dış Giyim': clothes.filter(c => c.category === 'Dış Giyim'),
      'Ayakkabı': clothes.filter(c => c.category === 'Ayakkabı'),
    };

    const getRandom = (arr) => arr && arr.length > 0 ? arr[Math.floor(Math.random() * arr.length)] : null;

    setCurrentOutfit({
      üst: locked.üst ? currentOutfit.üst : getRandom(categories['Üst Giyim']),
      alt: locked.alt ? currentOutfit.alt : getRandom(categories['Alt Giyim']),
      dis: locked.dis ? currentOutfit.dis : getRandom(categories['Dış Giyim']),
      ayakkabi: locked.ayakkabi ? currentOutfit.ayakkabi : getRandom(categories['Ayakkabı']),
    });
  };

  const saveOutfit = () => {
    if (!outfitName.trim()) {
      alert('Lütfen kombine bir isim verin!');
      return;
    }
    const newOutfit = {
      id: Date.now(),
      name: outfitName,
      items: { ...currentOutfit }
    };
    setOutfits([...outfits, newOutfit]);
    setOutfitName('');
    alert('Kombin başarıyla kaydedildi!');
  };

  return (
    <div className="min-h-screen bg-rose-50/50 text-slate-800 pb-12 font-sans">
      <header className="bg-white shadow-sm border-b border-rose-100 py-4 px-6 mb-8">
        <div className="max-w-4xl mx-auto flex flex-col sm:flex-row justify-between items-center gap-4">
          <h1 className="text-2xl font-bold text-rose-600">
            Kapsül Gardırop & Kombin
          </h1>
          <nav className="flex gap-2 bg-rose-50 p-1 rounded-xl">
            <button 
              onClick={() => setActiveTab('wardrobe')}
              className={`px-4 py-2 rounded-lg font-medium transition-all ${activeTab === 'wardrobe' ? 'bg-white text-rose-600 shadow-sm' : 'text-slate-600'}`}
            >
              Dolabım
            </button>
            <button 
              onClick={() => setActiveTab('generator')}
              className={`px-4 py-2 rounded-lg font-medium transition-all ${activeTab === 'generator' ? 'bg-white text-rose-600 shadow-sm' : 'text-slate-600'}`}
            >
              Kombin Sihirbazı
            </button>
            <button 
              onClick={() => setActiveTab('saved')}
              className={`px-4 py-2 rounded-lg font-medium transition-all ${activeTab === 'saved' ? 'bg-white text-rose-600 shadow-sm' : 'text-slate-600'}`}
            >
              Favorilerim ({outfits.length})
            </button>
          </nav>
        </div>
      </header>

      <main className="max-w-4xl mx-auto px-4">
        {activeTab === 'wardrobe' && (
          <div className="space-y-8">
            <div className="bg-white p-6 rounded-2xl shadow-sm border border-rose-100">
              <h2 className="text-lg font-semibold mb-4 text-rose-600">Dolaba Yeni Parça Ekle</h2>
              <form onSubmit={handleAddCloth} className="grid grid-cols-1 sm:grid-cols-2 lg:grid-cols-4 gap-4">
                <input 
                  type="text" 
                  placeholder="Parça Adı (Örn: Blazer)" 
                  value={name}
                  onChange={(e) => setName(e.target.value)}
                  className="px-4 py-2 border rounded-xl focus:outline-none focus:ring-2 focus:ring-rose-400"
                />
                <select 
                  value={category} 
                  onChange={(e) => setCategory(e.target.value)}
                  className="px-4 py-2 border rounded-xl bg-white"
                >
                  <option>Üst Giyim</option>
                  <option>Alt Giyim</option>
                  <option>Dış Giyim</option>
                  <option>Ayakkabı</option>
                  <option>Aksesuar</option>
                </select>
                <div className="flex items-center gap-2 px-3 py-2 border rounded-xl">
                  <span className="text-sm text-slate-500">Renk:</span>
                  <input 
                    type="color" 
                    value={color} 
                    onChange={(e) => setColor(e.target.value)}
                    className="w-full h-8 cursor-pointer rounded border-0 bg-transparent"
                  />
                </div>
                <button type="submit" className="bg-rose-500 hover:bg-rose-600 text-white font-medium py-2 px-4 rounded-xl shadow-md">
                  Ekle
                </button>
              </form>
            </div>

            <div className="bg-white p-6 rounded-2xl shadow-sm border border-rose-100">
              <h2 className="text-lg font-semibold mb-4 text-rose-600">Gardırop Envanterim ({clothes.length} Parça)</h2>
              {clothes.length === 0 ? (
                <p className="text-slate-400 text-center py-8">Henüz parça eklenmemiş.</p>
              ) : (
                <div className="grid grid-cols-1 sm:grid-cols-2 md:grid-cols-3 gap-4">
                  {clothes.map((item) => (
                    <div key={item.id} className="p-4 border rounded-xl flex items-center justify-between bg-slate-50">
                      <div className="flex items-center gap-3">
                        <span className="w-5 h-5 rounded-full border border-black/10" style={{ backgroundColor: item.color }} />
                        <div>
                          <h3 className="font-medium text-slate-800">{item.name}</h3>
                          <span className="text-xs text-rose-500 font-medium px-2 py-0.5 bg-rose-50 rounded-full">{item.category}</span>
                        </div>
                      </div>
                      <button onClick={() => handleDeleteCloth(item.id)} className="text-slate-400 hover:text-red-500 text-sm p-1">
                        Sil
                      </button>
                    </div>
                  ))}
                </div>
              )}
            </div>
          </div>
        )}

        {activeTab === 'generator' && (
          <div className="bg-white p-6 rounded-2xl shadow-sm border border-rose-100 space-y-6 text-center">
            <h2 className="text-xl font-bold text-slate-800">Rastgele Kombin Sihirbazı</h2>
            <p className="text-slate-500 text-sm">Beğendiğin parçaları kilitleyip rastgele kombin üret!</p>

            <div className="grid grid-cols-1 sm:grid-cols-2 gap-4 max-w-2xl mx-auto">
              {Object.entries(currentOutfit).map(([key, item]) => (
                <div key={key} className="p-4 border-2 border-dashed rounded-xl flex flex-col items-center justify-center gap-2 bg-slate-50">
                  <span className="text-xs font-bold uppercase tracking-wider text-rose-500">{key}</span>
                  {item ? (
                    <div className="flex items-center gap-2">
                      <span className="w-4 h-4 rounded-full border" style={{ backgroundColor: item.color }} />
                      <span className="font-medium">{item.name}</span>
                    </div>
                  ) : (
                    <span className="text-slate-400 text-sm">Seçilmedi</span>
                  )}
                  <button 
                    onClick={() => setLocked({ ...locked, [key]: !locked[key] })}
                    className={`text-xs px-3 py-1 rounded-full font-medium ${locked[key] ? 'bg-amber-500 text-white' : 'bg-slate-200 text-slate-600'}`}
                  >
                    {locked[key] ? 'Kilitli' : 'Kilitle'}
                  </button>
                </div>
              ))}
            </div>

            <button 
              onClick={generateOutfit}
              className="bg-rose-500 hover:bg-rose-600 text-white font-semibold py-3 px-8 rounded-xl shadow-lg mx-auto block"
            >
              Kombin Önerisi Üret
            </button>

            {(currentOutfit.üst || currentOutfit.alt) && (
              <div className="pt-6 border-t flex flex-col sm:flex-row justify-center gap-3 max-w-md mx-auto">
                <input 
                  type="text" 
                  placeholder="Kombin Adı" 
                  value={outfitName}
                  onChange={(e) => setOutfitName(e.target.value)}
                  className="px-4 py-2 border rounded-xl flex-1 focus:outline-none focus:ring-2 focus:ring-rose-400"
                />
                <button 
                  onClick={saveOutfit}
                  className="bg-emerald-500 hover:bg-emerald-600 text-white font-medium px-6 py-2 rounded-xl shadow-md"
                >
                  Kaydet
                </button>
              </div>
            )}
          </div>
        )}

        {activeTab === 'saved' && (
          <div className="bg-white p-6 rounded-2xl shadow-sm border border-rose-100 space-y-4">
            <h2 className="text-lg font-semibold text-rose-600">Kayıtlı Kombinlerim</h2>
            {outfits.length === 0 ? (
              <p className="text-slate-400 text-center py-8">Henüz kayıtlı kombin yok.</p>
            ) : (
              <div className="grid grid-cols-1 sm:grid-cols-2 gap-4">
                {outfits.map((outfit) => (
                  <div key={outfit.id} className="p-4 border rounded-xl bg-slate-50 space-y-2">
                    <div className="flex justify-between items-center">
                      <h3 className="font-bold text-rose-600">{outfit.name}</h3>
                      <button 
                        onClick={() => setOutfits(outfits.filter(o => o.id !== outfit.id))}
                        className="text-slate-400 hover:text-red-500 text-sm"
                      >
                        Sil
                      </button>
                    </div>
                    <div className="text-sm space-y-1 text-slate-600">
                      {Object.entries(outfit.items).map(([k, v]) => v ? (
                        <div key={k} className="flex items-center gap-2">
                          <span className="w-2.5 h-2.5 rounded-full" style={{ backgroundColor: v.color }} />
                          <span className="font-medium capitalize">{k}:</span> {v.name}
                        </div>
                      ) : null)}
                    </div>
                  </div>
                ))}
              </div>
            )}
          </div>
        )}
      </main>
    </div>
  );
}
