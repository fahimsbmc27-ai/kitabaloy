import React, { useMemo } from 'react';
import { BarChart, Bar, XAxis, YAxis, CartesianGrid, Tooltip, ResponsiveContainer, Cell } from 'recharts';
import { Book, Transaction } from '../types';
import { Trophy, Star, BookOpenText } from 'lucide-react';

interface AnalyticsProps {
  books: Book[];
  transactions: Transaction[];
}

export const Analytics: React.FC<AnalyticsProps> = ({ books, transactions }) => {
  const sortedBySales = useMemo(() => {
    return [...books].sort((a, b) => b.salesCount - a.salesCount);
  }, [books]);

  const topFive = sortedBySales.slice(0, 5);

  const chartData = useMemo(() => {
    return topFive.map(book => ({
      name: book.name.length > 10 ? book.name.substring(0, 10) + '...' : book.name,
      sales: book.salesCount,
      fullName: book.name
    }));
  }, [topFive]);

  const COLORS = ['#6366f1', '#10b981', '#f59e0b', '#ef4444', '#8b5cf6'];

  return (
    <div className="space-y-6">
      <div className="bg-white p-5 rounded-2xl shadow-sm border border-slate-100">
        <div className="flex items-center space-x-2 mb-4">
          <Trophy className="text-amber-500" size={20} />
          <h3 className="font-bold text-slate-700">সেরা বিক্রিত বইয়ের তালিকা</h3>
        </div>
        
        <div className="h-64 w-full">
          <ResponsiveContainer width="100%" height="100%">
            <BarChart data={chartData} layout="vertical" margin={{ left: -10, right: 30 }}>
              <CartesianGrid strokeDasharray="3 3" horizontal={true} vertical={false} stroke="#f1f5f9" />
              <XAxis type="number" hide />
              <YAxis dataKey="name" type="category" axisLine={false} tickLine={false} tick={{fontSize: 10, fill: '#64748b'}} width={80} />
              <Tooltip 
                cursor={{fill: 'transparent'}}
                content={({ active, payload }) => {
                  if (active && payload && payload.length) {
                    return (
                      <div className="bg-slate-800 text-white p-2 rounded-lg text-xs shadow-xl">
                        <p className="font-bold">{payload[0].payload.fullName}</p>
                        <p>বিক্রি: {payload[0].value} কপি</p>
                      </div>
                    );
                  }
                  return null;
                }}
              />
              <Bar dataKey="sales" radius={[0, 4, 4, 0]} barSize={30}>
                {chartData.map((entry, index) => (
                  <Cell key={`cell-${index}`} fill={COLORS[index % COLORS.length]} />
                ))}
              </Bar>
            </BarChart>
          </ResponsiveContainer>
        </div>
      </div>

      <div className="space-y-4">
        <h3 className="font-bold text-slate-700 flex items-center px-1">
          <Star className="text-amber-500 mr-2" size={18} fill="currentColor" /> র্যাঙ্কিং রিপোর্ট
        </h3>
        
        {sortedBySales.length === 0 ? (
          <p className="text-center py-10 text-slate-400">কোনো তথ্য নেই</p>
        ) : (
          sortedBySales.map((book, index) => (
            <div key={book.id} className="bg-white p-4 rounded-xl border border-slate-100 flex items-center justify-between shadow-sm">
              <div className="flex items-center space-x-4">
                <div className={`w-8 h-8 rounded-full flex items-center justify-center font-bold text-sm ${
                  index === 0 ? 'bg-amber-100 text-amber-600' : 
                  index === 1 ? 'bg-slate-200 text-slate-600' :
                  index === 2 ? 'bg-orange-100 text-orange-600' :
                  'bg-slate-50 text-slate-400'
                }`}>
                  {index + 1}
                </div>
                <div>
                  <h4 className="font-bold text-slate-800 text-sm leading-tight">{book.name}</h4>
                  <p className="text-xs text-slate-400">{book.library}</p>
                </div>
              </div>
              <div className="text-right">
                <p className="font-bold text-indigo-600">{book.salesCount}</p>
                <p className="text-[10px] text-slate-400 uppercase font-bold tracking-wider">কপি</p>
              </div>
            </div>
          ))
        )}
      </div>

      {/* Quick Summary Tip */}
      <div className="bg-indigo-50 border border-indigo-100 rounded-xl p-4 flex space-x-3">
        <div className="text-indigo-600">
          <BookOpenText size={24} />
        </div>
        <div>
          <h4 className="text-sm font-bold text-indigo-900">ব্যবসায়িক পরামর্শ</h4>
          <p className="text-xs text-indigo-700 mt-1 leading-relaxed">
            আপনার শীর্ষ ৫টি বই মোট বিক্রির {Math.round((topFive.reduce((s,b)=>s+b.salesCount, 0) / (books.reduce((s,b)=>s+b.salesCount, 0) || 1)) * 100)}% দখল করে আছে। এই বইগুলোর স্টক পর্যাপ্ত রাখার চেষ্টা করুন।
          </p>
        </div>
      </div>
    </div>
  );
};
